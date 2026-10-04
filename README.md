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

**Pavadinimas ir atsakomybė:** Darbų rotacijos ir skolų optimizavimo moduliai. Moduliai sprendžia du pagrindinius uždavinius: sąžiningą buities darbų paskirstymą atsižvelgiant į istorinį krūvį bei narių pasiekiamumą (išvykimus) ir tarpusavio skolų supaprastinimą (angl. *debt simplification*).

**Logika, kurią reikės projektuoti ir testuoti:**
1. *Darbų skirstymas:* Algoritmas vertina kiekvieno nario per pastarąsias savaites surinktų taškų sumą ir esamą statusą (namuose / išvykęs). Darbai prioriteto tvarka skiriami mažiausiai prisidėjusiems ir esantiems namuose.
2. *Skolų sutraukimas:* Orientuoto skolos grafo analizė ir tranzityvių skolų sutraukimas (pvz., jei A skolingas B, o B skolingas C, sistema sugeneruoja tiesioginę skolą iš A į C), taip redukuojant bendrą visos grupės pavedimų skaičių.

**Įvestis:** Narių sąrašas `[A, B, C]`, narių būsenos (pvz., `C` išvykęs iki sekmadienio), atliktų darbų istorija bei neapmokėtų išlaidų įrašai. Pavyzdys išlaidoms: A sumokėjo 30 € už B ir C (po 10 €), B sumokėjo 10 € už A.

**Išvestis:** Savaitinis darbų tvarkaraštis su priskirtais atsakingais asmenimis ir optimizuotas atsiskaitymų sąrašas. Pavyzdys: „C perveda A 10 €“ (vietoje kelių atskirų pervedimų pirmyn-atgal).

**Veikimo eiga:**
1. Surenkamas aktyvių grupės narių sąrašas, jų pasiekiamumo statusas (ar nėra pažymėję išvykimo), išlaidų čekiai ir atliktų darbų taškų istorija.
2. Apskaičiuojamas kiekvieno nario grynasis piniginis balansas (išleista suma minus gauta nauda) ir darbų krūvio taškai.
3. Pritaikomas skolų sutraukimo algoritmas, rūšiuojant balansus nuo didžiausio skolininko iki didžiausio kreditoriaus.
4. Pritaikoma darbų paskirstymo taisyklė: atmetami išvykę nariai, o likusiems darbai išdalinami pradedant nuo mažiausiai taškų turinčio nario.
5. Grąžinamas suformuotas tvarkaraštis ir mokėjimų planas.

### Taisyklės arba sprendimo žingsniai

1. **Išvykimo ir kompensavimo taisyklė:** Naudotojas, pažymėjęs išvykimo laikotarpį, neįtraukiamas į to laikotarpio darbų skirstymą. Tačiau jam grįžus, sistema automatiškai priskiria jį į eilės priekį pirmiems artėjantiems darbams, kad išlygintų bendrą taškų vidurkį.
2. **Darbų rotacijos taisyklė:** Sunkaus darbo (vertinamo didesniu taškų skaičiumi, pvz., >15) atlikimas suteikia nariui imunitetą nuo kito sunkaus darbo paskyrimo ateinančią savaitę, net jei jo bendras taškų skaičius yra mažiausias.
3. **Skolų sutraukimo taisyklė:** Jei nario A skola nariui B yra lygi arba didesnė už nario B skolą nariui C, sistema panaikina tarpinį nario B balansą ir paverčia tai tiesiogine nario A skola nariui C.

### Scenarijai būsimiems testams

| Scenarijus | Pradinės sąlygos ir konkreti įvestis | Veiksmas | Tikslus laukiamas rezultatas |
|---|---|---|---|
| Įprastas atvejis | 3 kambariokai (A, B, C). A sumokėjo 30 € už visus. B sumokėjo 10 € už A. C išvykimų neturi. | Paleidžiamas skolų optimizavimas | B skolos likutis 0 €, C skolingas A 10 €. Sugeneruotas tik 1 pervedimas grupėje. |
| Ribinis atvejis arba konfliktas | Narys A turi mažiausiai taškų, bet yra pažymėjęs išvykimą šiai savaitei. Liko 1 nepriskirtas darbas. | Paleidžiamas darbų skirstymas | Sistema praleidžia narį A ir priskiria darbą nariui B (kuris turi antrą mažiausią taškų skaičių). |
| Klaida arba neįmanomas rezultatas | Visi būsto nariai pažymėti kaip „Išvykę“ esamą savaitę, bandoma sugeneruoti savaitės valymo grafiką. | Paleidžiamas darbų skirstymas | Grąžinamas pranešimas „Nėra pasiekiamų narių darbams atlikti“, tvarkaraštis lieka tuščias. |

**Jei modulis naudoja AI:** Netaikoma. Pačiame sistemos funkcionalume (algoritmuose) dirbtinis intelektas nebus naudojamas.

## 4. Kokybės atributas

**Pasirinktas atributas:** Testuojamumas (angl. *Testability*).

**Kodėl svarbus šiai sistemai:** Skolų optimizavimo ir darbų rotacijos (ypač vertinant išvykimus ir istorinius taškus) algoritmai turi griežtą, matematiškai pagrįstą logiką. Jei šis modulis bus per daug susietas su duomenų baze ar naudotojo sąsaja, bet koks naujos taisyklės pridėjimas gali nepastebimai sugadinti skaičiavimus. Atskyrus logiką ir užtikrinus testuojamumą, skaičiavimų teisingumą bus galima patikrinti greitai ir patikimai.

**Tikrinimo scenarijus ir sąlygos:** Vykdomas automatizuotų vieneto (*unit*) testų rinkinys, apimantis įvairias skolų grandines (nuo 2 iki 10 narių) ir darbų rotacijos atvejus (įskaitant kelių narių atostogų persidengimus), neprisijungiant prie duomenų bazės.

**Sėkmės kriterijus:** 100% automatizuotų testų praėjimas (*pass rate*), o kodo padengimas testais (*code coverage*) pagrindiniame modulyje siekia ne mažiau kaip 85%.

**Numatytas projektavimo sprendimas:** Skolų ir darbų skaičiavimo logika bus izoliuota į „grynąsias funkcijas“ (angl. *pure functions*), kurios priima tik duomenų struktūras (sąrašus, objektus) ir grąžina rezultatą be šalutinių efektų, tiesiogiai nekviesdamos duomenų bazės.

**Kaip patikrinsiu vėlesniame etape:** Paleisiu automatinių testų komandą (pvz., `npm test` arba `pytest`) ir sugeneruosiu kodo padengimo testais ataskaitą, įrodančią reikalavimo įvykdymą.

**Sprendimo kaina arba ribojimas:** Reikės papildomo laiko paruošti duomenų perdavimo objektus (DTO) ir struktūriškai atskirti verslo logiką nuo karkaso (framework) valdiklių bei duomenų bazės modelių.

## 5. Pradinė sistemos struktūra

### Paprasta schema

```text
+-------------------------------------------------------+
|               Naudotojo sąsaja (Frontend)             |
|(Vaizduoja grafikus, leidžia įvesti išlaidas/atostogas)|
+---------------------------+---------------------------+
                            |
                            | API Užklausos (JSON)
                            v
+-------------------------------------------------------+
|              Backend Valdiklis (API Layer)            |
|        (Užklausų priėmimas, saugumo tikrinimas)       |
+---------------------------+---------------------------+
                            |
                            | Duomenų perdavimas
                            v
+-------------------------------------------------------+
|       Pagrindiniai moduliai (Core Engines)            |
| (Rotacijos, atostogų įvertinimo ir skolų algoritmai)  |
+---------------------------+---------------------------+
                            |
                            | Skaitymas / Rašymas
                            v
+-------------------------------------------------------+
|                      Duomenų bazė                     |
+-------------------------------------------------------+
                            v
+-------------------------------------------------------+
|             Duomenų bazė (Persistence)                |
|       (Saugo narius, istoriją, čekius, išvykimus)     |
+-------------------------------------------------------+
```
| Sistemos dalis | Atsakomybė |
|---|---|
| Naudotojo sąsaja (Frontend) | Vaizduoja grafikus, leidžia vartotojui įvesti duomenis ir žymėti išvykimus. |
| Backend Valdiklis (API Layer) | Priima užklausas iš kliento, užtikrina saugumą ir nukreipia į logiką. |
| Pagrindiniai moduliai (Core Engines) | Vykdo rotacijos, atostogų įvertinimo ir skolų optimizavimo algoritmus. |
| Duomenų bazė (Persistence) | Saugomi naudotojų, grupių, atliktų darbų, išlaidų ir taškų įrašai. |

**Planuojamos technologijos ir pasirinkimo priežastys:** Frontend daliai planuojama naudoti *React.js*, nes tai leidžia greitai kurti interaktyvias sąsajas. Backend daliai – *Node.js* arba *Python*, nes abi kalbos puikiai tinka greitam API ir algoritmų prototipavimui. Duomenų bazei – *PostgreSQL* dėl reliacinės struktūros patikimumo.

## 6. AI panaudojimas

### AI rengiant šį dokumentą

| Priemonė ir užduotis | Ką panaudojau | Ką atmečiau arba perrašiau ir kodėl | Kaip patikrinau |
|---|---|---|---|
| AI asistentas (LLM) - dokumento formatavimui redagavimui | Sugeneruotą dokumento struktūrą ir formuluotes pagal šabloną. | Pakeičiau dalį teksto performuluodamas ar pakeisdamas savo žodžiais ir savomis mintimis. | Perskaičiau ir sulyginau su reikalavimų šablono aprašu, pakeičiau pagal savo pageidavimus. |

### Planuojamas AI naudojimas kuriant sistemą

**Kur ir kam naudosiu AI:** Nors pačioje sistemoje vartotojams AI nebus pasiekiamas, AI (pvz., „GitHub Copilot“ ar „ChatGPT“) aktyvai naudosiu kaip pagalbinį įrankį programavimo metu – rašant pradinį programinį kodą, pagreitinant sudėtingesnių algoritmų (grafų ciklų) realizaciją, ieškant klaidų (angl. *debugging*) ir generuojant „unit“ testų šablonus.

**Kaip tikrinsiu pasiūlymus ir sugeneruotą kodą:** AI sugeneruotą kodą tikrinsiu atlikdamas savarankišką kodo peržiūrą (*code review*), atidžiai vertindamas ribinius scenarijus ir vykdydamas automatizuotus vieneto testus, kad garantuočiau logikos tikslumą.

**Ar AI bus sistemos funkcionalumo dalis:** Ne. Sistemoje galutiniam naudotojui AI funkcijų nebus.

## 7. Tolesnių darbų planas

| Darbas | Apčiuopiamas rezultatas | Planuojama darbų seka |
|---|---|---|
| Pagrindinių modulių (skolų ir darbų rotacijos) programavimas | Veikiantys algoritmai, padengti vieneto testais | 1 |
| Sistemos architektūros ir duomenų bazės projektavimas | Duomenų bazių schemos ir API specifikacija | 2 |
| Vartotojo sąsajos (frontend) sukūrimas prototipui | Interaktyvios formos duomenims įvesti ir rezultatams peržiūrėti | 3 |

**Būsimo prototipo veikimo scenarijus:** Norėsiu pademonstruoti, kaip naudotojas įveda kelių kambariokų bendras išlaidas ir pažymi vieno iš narių atostogas. Paspaudus „Generuoti tvarkaraštį“, sistema grąžins matematiškai optimizuotą mokėjimų planą (pvz., išves vieną pervedimą vietoje kelių susipynusių skolų) ir suformuos kitos savaitės valymo grafiką, kuriame atostogaujantis asmoe nebus priskirtas prie jokių užduočių.

| Rizika arba neaiškumas | Kaip patikrinsiu arba sumažinsiu |
|---|---|
| Skolų optimizavimo algoritmas gali „užsiciklinti“ arba nesuveikti su sudėtingais tarpusavio skolininkų ryšiais. | Sukursiu specializuotus „unit“ testus išskirtiniams atvejams (pvz., cikliniams grafams) ir patikrinsiu logikos rezultatus rankiniu būdu. |
| API duomenų struktūros gali prasilenkti derinant *Frontend* ir *Backend* dalis. | Iš anksto susitarsiu ir aprašysiu griežtus duomenų perdavimo objektų (DTO) ir JSON struktūrų formatus. |
