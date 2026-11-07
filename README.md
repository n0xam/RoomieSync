# RoomieSync – Kambariokų buities darbų ir bendrų išlaidų valdymo sistema

Kursinio darbo I dalis: projektavimo dokumentas

## 1. Problema ir idėja

**Sistema vienu sakiniu:** Sistema skirta kartu gyvenantiems kambariokams, padedanti automatiškai ir sąžiningai paskirstyti buities darbus bei supaprastinti bendrų išlaidų ir skolų išlyginimą.

**Problema ir dabartinis procesas:** Šiuo metu kambariokai buities darbus dažniausiai derina žodžiu arba susirašinėjimo programėlėse, o išlaidas seka „Excel“ lentelėse arba skaičiuoja ranka. Šis procesas turi akivaizdžių trūkumų: kyla konfliktai dėl nesąžiningo ar nevienodo darbų krūvio, pamirštami atlikti darbai, neseka, kas ir kada yra išvykęs, o susikaupusios skolos tampa painios ir reikalauja daugybės tarpusavio bankinių pervedimų.

**Nauda:** Naudotojams sumažės buitinių konfliktų rizika, bus užtikrintas skaidrus ir sąžiningas darbų rotavimas (atsižvelgiant į išvykimus) bei iki minimumo sumažintas atliekamų piniginių pervedimų skaičius.

**Naudotojai:** Kartu gyvenantys studentai ar būsto nuomininkai (kambariokai), kurie kuria grupes, registruoja atliktus darbus, žymi savo išvykimus, įveda bendras išlaidas ir peržiūri subalansuotą tvarkaraštį bei atsiskaitymų ataskaitas.

**Prielaidos:** Daroma prielaida, kad visi vieno būsto kambariokai aktyviai naudojasi ta pačia sistema, įvedami išlaidų duomenys yra teisingi, o buities darbų sudėtingumas vertinamas iš anksto visų narių sutartais taškais.

## 2. Apimtis

| Funkcija | Ką naudotojas galės atlikti | Pagrindinis modulis ar pagalbinė funkcija |
|---|---|---|
| Buities darbų rotacijos variklis | Automatiškai paskirstyti darbus pagal surinktus taškus ir pasiekiamumą | **Pagrindinis modulis** |
| Išlaidų ir skolų balansavimo variklis | Supaprastinti tarpusavio skolas ir redukuoti pervedimų skaičių grafų teorijos pagrindu | **Pagrindinis modulis** |
| Būsto grupės ir narių valdymas | Sukurti grupę, pakviesti kambariokus, tvarkyti jų nustatymus | Pagalbinė funkcija |
| Darbų ir išvykimų žymėjimas | Pažymėti įvykdytus darbus (gauti taškus) bei nurodyti laikotarpį, kada asmuo bus išvykęs iš būsto | Pagalbinė funkcija |

**Į kursinio darbo apimtį neįeina:** Tiesioginė integracija su bankų sistemomis (mokėjimų atlikimas pačioje programėlėje), kelių skirtingų valiutų konvertavimas realiu laiku, automatinis kokybės tikrinimas, ar darbas tikrai atliktas gerai. Taip pat neįeina išlaidų registravimas su AI (automatizuotas čekių nuskaitymas).

## 3. Pagrindiniai moduliai

**Pavadinimas ir atsakomybė:** Darbų rotacijos ir skolų optimizavimo moduliai. Moduliai sprendžia du pagrindinius uždavinius: sąžiningą buities darbų paskirstymą atsižvelgiant į ankstesnį darbų krūvį bei narių pasiekiamumą (išvykimus) ir tarpusavio skolų supaprastinimą (angl. *debt simplification*).

**Logika, kurią reikės projektuoti ir testuoti:**
1. *Darbų skirstymas:* Algoritmas vertina kiekvieno nario per paskutines 4 užbaigtas kalendorines savaites surinktų taškų sumą ir pasiekiamumą konkretaus darbo atlikimo metu. Taškai pridedami tik patvirtinus darbo atlikimą, pagal tam darbui iš anksto nustatytą vertę. Išvykimo dienomis neatlikti darbai taškų nesuteikia. Skirstant darbus taip pat vertinamas imunitetas sunkiems darbams ir grįžusio nario prioritetas.
2. *Skolų sutraukimas:* Pagal visus neapmokėtus įrašus apskaičiuojamas kiekvieno nario grynasis balansas, o tada sudaromas mokėjimų planas, išsaugantis šiuos balansus. Pavyzdžiui, jei A skolingas B 20 €, o B skolingas C 10 €, vietoj pradinių skolų lieka A→B 10 € ir A→C 10 €. B vis dar turi gauti 10 €, todėl jo balansas neišnyksta.

**Įvestis:** Narių sąrašas `[A, B, C]`, narių būsenos ir išvykimų laikotarpiai (pvz., `C` išvykęs iki sekmadienio), darbų atlikimo datos, jų taškų vertės, paskutinio paskyrimo datos bei neapmokėtų išlaidų įrašai. Pavyzdys išlaidoms: A sumokėjo 30 € už visus tris narius (po 10 €), B sumokėjo 10 € už A.

**Išvestis:** Savaitinis darbų tvarkaraštis su priskirtais atsakingais asmenimis ir supaprastintas atsiskaitymų sąrašas. Pavyzdžiui, pateiktoms išlaidoms užtenka vieno mokėjimo: „C perveda A 10 €“.

**Veikimo eiga:**
1. Surenkamas grupės narių sąrašas, jų pasiekiamumas pagal konkrečių darbų datas, neapmokėtų išlaidų įrašai ir darbų istorija.
2. Apskaičiuojamas kiekvieno nario grynasis piniginis balansas (už kitus sumokėtos sumos minus kitų už jį apmokėtos sumos) ir per paskutines 4 užbaigtas kalendorines savaites uždirbti darbų taškai.
3. Skolų optimizavimo modulis sudaro skolininkų ir kreditorių sąrašus, parenka mokėjimus ir kiekviename žingsnyje atnaujina likusias sumas, nekeičiant narių galutinių grynųjų balansų.
4. Darbų paskirstymo modulis kiekvienam darbui pašalina išvykusius narius; sunkiems darbams papildomai pašalina turinčius imunitetą. Iš likusių kandidatų pirmiausia renkamas grįžimo prioritetą turintis narys, tada mažiausiai taškų surinkęs narys; lygybės atveju taikoma papildoma rotacijos taisyklė.
5. Grąžinamas suformuotas tvarkaraštis ir mokėjimų planas. Jeigu darbui nelieka tinkamų narių, pateikiamas aiškus pranešimas.

### Taisyklės arba sprendimo žingsniai

1. **Taškų apskaitos taisyklė:** Darbų krūviui palyginti naudojami tik paskutinių 4 užbaigtų kalendorinių savaičių (pirmadienis–sekmadienis) taškai prieš generuojamą savaitę. Už kiekvieną patvirtintą darbą taškai užskaitomi pagal atlikimo datą. Neatlikti darbai ir išvykimo laikotarpiai taškų nesuteikia.
2. **Išvykimo ir grįžimo taisyklė:** Išvykęs narys neskiriamas darbui, kurio atlikimo data patenka į jo išvykimo laikotarpį. Grįžusiam nariui suteikiamas vienkartinis prioritetas pirmam po grįžimo tinkamam darbui per artimiausią savaitę; jeigu tam darbui taikomas imunitetas, prioritetas išlieka kitam tinkamam darbui iki tos savaitės pabaigos. Šis prioritetas nesuteikia teisės apeiti imuniteto.
3. **Sunkių darbų imuniteto taisyklė:** Atlikus sunkų darbą (daugiau kaip 15 taškų), narys kitą kalendorinę savaitę negali gauti kito sunkaus darbo, net jei turi mažiausiai taškų arba ką tik grįžo iš išvykimo. Lengviems darbams imunitetas netaikomas.
4. **Prioritetų ir vienodų taškų taisyklė:** Kandidatai atrenkami tokia tvarka: (1) turi būti pasiekiami; (2) sunkiems darbams negali turėti imuniteto; (3) pirmenybė suteikiama grįžimo prioritetą turinčiam nariui; (4) jei tokių kandidatų keli arba jų nėra, pasirenkamas mažiausiai taškų surinkęs narys; (5) surinkus vienodai taškų, parenkamas seniausiai darbą gavęs narys, o jei ir ši data sutampa – pagal pastovią nario ID didėjimo tvarką. Panaudotas grįžimo prioritetas panaikinamas.
5. **Skolų sutraukimo taisyklė:** Skolos supaprastinamos išsaugant kiekvieno nario grynąjį balansą. Jei A skolingas B 20 €, o B skolingas C 10 €, sugeneruojami mokėjimai A→B 10 € ir A→C 10 €. A iš viso sumoka 20 €, B gauna 10 €, C gauna 10 €. Jei sumos būtų vienodos (pvz., po 10 €), pakaktų vieno A→C 10 € mokėjimo, o B grynasis balansas būtų 0 €.

### Scenarijai būsimiems testams

| Scenarijus | Pradinės sąlygos ir konkreti įvestis | Veiksmas | Tikslus laukiamas rezultatas |
|---|---|---|---|
| Įprastas skolų optimizavimas | 3 kambariokai (A, B, C). A sumokėjo 30 € už visus (po 10 €). B sumokėjo 10 € už A. | Paleidžiamas skolų optimizavimas. | B grynasis balansas 0 €, C skolingas A 10 €. Sugeneruojamas vienas mokėjimas C→A 10 €. |
| Nelygių skolų sutraukimas | A skolingas B 20 €, o B skolingas C 10 €. | Paleidžiamas skolų optimizavimas. | Sugeneruojami du mokėjimai: A→B 10 € ir A→C 10 €. B grynasis balansas +10 €, o ne 0 €. |
| Išvykusio nario praleidimas | Per paskutines 4 užbaigtas savaites A surinko 4 tšk., B – 8 tšk., C – 12 tšk. A išvykęs visą planuojamą savaitę; B ir C pasiekiami. Yra vienas 5 tšk. vertės darbas; imuniteto ir grįžimo prioriteto niekas neturi. | Paleidžiamas darbų skirstymas. | A neįtraukiamas; darbas priskiriamas B, nes jis turi mažiausiai taškų iš pasiekiamų narių (8 tšk.). |
| Vienodų taškų konfliktas | A ir B turi po 8 tšk., C – 12 tšk. Visi pasiekiami, niekas neturi grįžimo prioriteto ar imuniteto. A paskutinį darbą gavo prieš 14 dienų, B – prieš 7 dienas. Skirstomas 5 tšk. darbas. | Paleidžiamas darbų skirstymas. | Darbas priskiriamas A, nes esant vienodiems taškams A darbą gavo seniau. |
| Grįžimo prioriteto ir imuniteto konfliktas | A ką tik grįžo ir turi vienkartinį prioritetą, bet praėjusią savaitę atliko 20 tšk. darbą, todėl turi imunitetą sunkiam darbui. A turi 24 tšk., B – 6 tšk., C – 9 tšk. Visi pasiekiami; B ir C imuniteto neturi. Skirstomas 20 tšk. darbas. | Paleidžiamas darbų skirstymas. | A negali gauti sunkaus darbo nepaisant grįžimo prioriteto. Darbas priskiriamas B (6 tšk.). A prioritetas išlieka kitam tinkamam darbui tą savaitę. |
| Klaida arba neįmanomas rezultatas | A, B ir C išvykę visą planuojamą savaitę; numatytas vienas 5 tšk. valymo darbas. | Paleidžiamas darbų skirstymas. | Grąžinamas pranešimas „Nėra pasiekiamų narių darbams atlikti“, tvarkaraštis lieka tuščias. |

**Jei modulis naudoja AI:** Netaikoma. Pačiame sistemos funkcionalume (algoritmuose) dirbtinis intelektas nebus naudojamas.

## 4. Kokybės atributas

**Pasirinktas atributas:** Testuojamumas (angl. *Testability*).

**Kodėl svarbus šiai sistemai:** Skolų optimizavimo ir darbų rotacijos (ypač vertinant išvykimus ir istorinius taškus) algoritmai turi griežtą, matematiškai pagrįstą logiką. Jei šis modulis bus per daug susietas su duomenų baze ar naudotojo sąsaja, bet koks naujos taisyklės pridėjimas gali nepastebimai sugadinti skaičiavimus. Atskyrus logiką ir užtikrinus testuojamumą, skaičiavimų teisingumą bus galima patikrinti greitai ir patikimai.

**Tikrinimo scenarijus ir sąlygos:** Vykdomas automatizuotų vieneto (*unit*) testų rinkinys, apimantis įvairias skolų grandines (nuo 2 iki 10 narių) ir darbų rotacijos atvejus (įskaitant kelių narių atostogų persidengimus), neprisijungiant prie duomenų bazės.

**Sėkmės kriterijus:** 100% automatizuotų testų praėjimas (*pass rate*), o kodo padengimas testais (*code coverage*) pagrindiniame modulyje siekia ne mažiau kaip 85%.

**Numatytas projektavimo sprendimas:** Skolų optimizavimo ir darbų rotacijos algoritmai bus realizuoti atskirose Java klasėse, nepriklausomose nuo duomenų bazės ir naudotojo sąsajos. Spring Boot servisai bus atsakingi už duomenų paruošimą ir algoritmų iškvietimą. Tai leis pagrindinę verslo logiką testuoti atskirai, naudojant JUnit 5, o prireikus imituoti priklausomybes su Mockito.

**Kaip patikrinsiu vėlesniame etape:** Naudojant Maven bus paleidžiami JUnit 5 vieneto testai (pvz., `mvn test`). Kodo padengimui matuoti planuojama naudoti JaCoCo. Bus tikrinama, ar visi testai sėkmingai įvykdomi ir ar pagrindinių algoritmų kodo padengimas siekia ne mažiau kaip 85 %.

**Sprendimo kaina arba ribojimas:** Reikės papildomo laiko paruošti duomenų perdavimo objektus (DTO) ir struktūriškai atskirti verslo logiką nuo karkaso (framework) valdiklių bei duomenų bazės modelių.

## 5. Pradinė sistemos struktūra

### Paprasta schema

```text
+------------------------------------------------------+
|                Naudotojo sąsaja                      |
|          HTML, CSS, Bootstrap, Thymeleaf             |
|       (Darbų grafikai, išlaidos, narių valdymas)      |
+--------------------------+---------------------------+
                           |
                           | HTTP užklausos
                           v
+------------------------------------------------------+
|                Spring Boot (Java)                    |
|                                                      |
|  +------------------------------------------------+  |
|  |           Controllers (Spring Web)             |  |
|  |     (Užklausų priėmimas ir apdorojimas)         |  |
|  +----------------------+-------------------------+  |
|                         |                            |
|  +----------------------v-------------------------+  |
|  |             Services (Verslo logika)            |  |
|  |   (Darbų rotacija ir skolų optimizavimas)       |  |
|  +----------------------+-------------------------+  |
|                         |                            |
|  +----------------------v-------------------------+  |
|  |        Spring Data JPA / Hibernate             |  |
|  |          (Duomenų bazės prieiga)               |  |
|  +------------------------------------------------+  |
+--------------------------+---------------------------+
                           |
                           | SQL užklausos
                           v
+------------------------------------------------------+
|                    PostgreSQL                        |
|     (Nariai, grupės, darbai, išlaidos, istorija)      |
+------------------------------------------------------+
```

| Sistemos dalis | Atsakomybė |
|---|---|
| Naudotojo sąsaja (Frontend) | Leidžia naudotojams per naršyklę registruoti išlaidas, peržiūrėti darbų grafikus, valdyti grupės narius ir žymėti išvykimus. Sąsaja pritaikoma kompiuteriams ir mobiliesiems įrenginiams. |
| Controllers (Spring Web) | Priima HTTP užklausas, perduoda duomenis verslo logikos sluoksniui ir grąžina naudotojui rezultatus. |
| Services (Verslo logika) | Įgyvendina darbų paskirstymo ir skolų optimizavimo algoritmus, atsižvelgiant į nustatytas taisykles. |
| Spring Data JPA / Hibernate | Užtikrina Java objektų susiejimą su duomenų bazės lentelėmis ir duomenų saugojimą bei nuskaitymą. |
| PostgreSQL | Saugo kambariokų grupes, narius, išlaidas, darbų istoriją, taškus ir išvykimų laikotarpius. |

**Planuojamos technologijos ir pasirinkimo priežastys:** Sistemos Backend daliai planuojama naudoti Java 21 ir Spring Boot karkasą, kuris leis kurti žiniatinklio programą ir atskirti pagrindinių algoritmų logiką nuo naudotojo sąsajos. Frontend daliai bus naudojami HTML, CSS, Bootstrap ir Thymeleaf, leidžiantys sukurti prie skirtingų ekrano dydžių prisitaikančią sąsają, pasiekiamą tiek kompiuteriuose, tiek mobiliuosiuose įrenginiuose. Duomenims saugoti pasirinkta PostgreSQL reliacinė duomenų bazė, o darbui su ja – Spring Data JPA ir Hibernate. Pagrindinių algoritmų testavimui bus naudojami JUnit 5 ir Mockito. Projekto priklausomybėms valdyti bus naudojamas Maven, o versijų kontrolei – Git. Vėlesniuose etapuose planuojama apsvarstyti Spring Security autentifikavimui ir Docker Compose programos paleidimo supaprastinimui.

## 6. AI panaudojimas

### AI rengiant šį dokumentą

| Priemonė ir užduotis | Ką panaudojau | Ką atmečiau arba perrašiau ir kodėl | Kaip patikrinau |
|---|---|---|---|
| „ChatGPT“ (OpenAI) – dokumento struktūros, formatavimo ir formuluočių rengimui bei redagavimui. | Panaudojau pasiūlytą dokumento struktūrą ir dalį formuluočių pagal užduoties reikalavimus. | Dalį formuluočių perrašiau savais žodžiais; algoritmų taisykles ir testų rezultatus patikslinau, kad būtų aiškūs ir neprieštaringi. | Palyginau tekstą su dėstytojo pateiktais reikalavimais, skolų skaičiavimus patikrinau rankiniu būdu, o darbų skirstymo taisykles – pagal konkrečius scenarijus. |

### Planuojamas AI naudojimas kuriant sistemą

**Kur ir kam naudosiu AI:** Nors pačioje sistemoje vartotojams AI nebus pasiekiamas, AI (pvz., „GitHub Copilot“ ar „ChatGPT“) aktyviai naudosiu kaip pagalbinį įrankį programavimo metu – rašant pradinį programinį kodą, pagreitinant sudėtingesnių algoritmų (grafų ciklų) realizaciją, ieškant klaidų (angl. *debugging*) ir generuojant „unit“ testų šablonus.

**Kaip tikrinsiu pasiūlymus ir sugeneruotą kodą:** AI sugeneruotą kodą tikrinsiu atlikdamas savarankišką kodo peržiūrą (*code review*), atidžiai vertindamas ribinius scenarijus ir vykdydamas automatizuotus vieneto testus, kad garantuočiau logikos tikslumą.

**Ar AI bus sistemos funkcionalumo dalis:** Ne. Sistemoje galutiniam naudotojui AI funkcijų nebus.

## 7. Tolesnių darbų planas

| Darbas | Apčiuopiamas rezultatas | Planuojama darbų seka |
|---|---|---|
| Sistemos architektūros ir duomenų bazės projektavimas | Suprojektuota PostgreSQL duomenų bazės schema, pagrindinės Java esybės ir Spring Boot projekto struktūra | 1 |
| Pagrindinių algoritmų įgyvendinimas | Veikiantys darbų rotacijos ir skolų optimizavimo algoritmai su JUnit 5 vieneto testais | 2 |
| Backend funkcionalumo kūrimas | Spring Boot servisai ir valdikliai, leidžiantys valdyti narius, darbus, išlaidas ir išvykimus | 3 |
| Naudotojo sąsajos kūrimas | Naudojant Thymeleaf ir Bootstrap sukurta žiniatinklio sąsaja, pritaikyta kompiuteriams ir telefonams | 4 |
| Sistemos integravimas ir testavimas | Sujungtos sistemos dalys, patikrinti pagrindiniai veikimo scenarijai ir ištaisytos nustatytos klaidos | 5 |

**Būsimo prototipo veikimo scenarijus:** Naudotojas per žiniatinklio sąsają sukuria kambariokų grupę, įveda bendras išlaidas ir pažymi vieno nario išvykimo laikotarpį. Sistema, naudodama Spring Boot realizuotus algoritmus, apskaičiuoja narių tarpusavio skolų balansus, sugeneruoja supaprastintą mokėjimų planą ir paskirsto ateinančios savaitės buities darbus pagal narių pasiekiamumą bei surinktus taškus. Rezultatai pateikiami naršyklėje, o duomenys išsaugomi PostgreSQL duomenų bazėje.

| Rizika arba neaiškumas | Kaip patikrinsiu arba sumažinsiu |
|---|---|
| Spring Boot ir Spring Data JPA naudojimas gali pareikalauti daugiau laiko dėl ribotos praktinės patirties. | Pradėsiu nuo nedidelio prototipo su viena esybe ir paprastomis duomenų bazės operacijomis, o vėliau palaipsniui pridėsiu sudėtingesnes funkcijas. |
