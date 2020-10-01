---
layout: default
permalink: /blog/cyrillic-to-latin-and-latin-to-cyrillic-javascript-library.html
image: js.png
description: CyrLatConverter je javascript biblioteka koji radi preslovljavanje iz ćirilice u latinicu i obrnuto. Pogledajte ovaj tutorijal i primer upotrebe biblioteke.
category: RS, Intermediate, VanillaJS, JavaScript
comments: true
---

# CyrLatConverter - JavaScript biblioteka za prebacivanje teksta u ćirilicu ili latinicu

CyrLatConverter je JavaScript biblioteka koji radi transliteraciju sadržaja iz latinince u ćirilicu, kao i iz ćirilice u latinicu. Transliteracija je relativno brza, i na prosečnim web stranicama, na računarima prosečne konfiguracije, brzina se meri u nekoliko desetina milisekundi. Prednost korišćenja jQuery plugin-a za transliteraciju je taj što nema potrebe za održavanjem dve različite verzije za ćirilicu i latinicu, kao i indeksiranje stranice od strane pretraživača može biti na latinici, a onda dinamički prebaciti sadržaj u ćirilicu.  

Neke od osobina CyrLatConverter-a:

- Pamćenje poslednje transliteracije u cookie, i automatska transliteracija pri ponovnoj poseti.
- Mogućnost transliteracija samo određenih delova teksta, ali takođe i cele stranice.
- Unutar dela teksta koji učestvuje u transliteraciji možemo vrlo lako obeležiti i delove teksta za koje ne želimo da učestvuju u transliteraciji.
- Pokretanje transliteracije klikom na dugme, ili mogućnost dodavanja hash taga za kreiranje linkova koji će automatski izvršiti transliteraciju. (Npr. www.mojsajt/page.html#cyr)
- Benchmark - merenje vremena koje je potrebno za izvršavanje skripte (približno vreme).
- Slova koja u latinici imaju dvoznak u ćirilici će se napisati uz pomoć jednog slova (Npr. Nj => Њ) ako takva transliteracija postoji.
- Izdvojen konfiguracioni fajl koji korisnik može prilagoditi svojim potrebama. Bitno je napomenuti da se konfiguracioni fajl automatski minifikuje sa bibliotekom u jedan krajnji fajl, cyrlatconverter.min.js.
- U izdvojenom konfiguracionom fajlu postoji spisak engleskih reči koje se često koriste u kombinaciji sa srpskim jezikom, a nije potrebna transliteracija takvih reči.
- U izdvojenom konfiguracionom fajlu možemo navesti reči koje ne želimo da imaju spajanje dva slova latinice u jedno ćirilično (Npr. "Njemačka" => "Нјемачка", a ne "Њемачка").
- Takodje se u izdvojenom konfiguracionom fajlu mogu navesti i osnove reči kod kojih treba ignorisati spajanje dva slova latinice u jedno ćirilično, a biblioteka onda neće spajati slova u svim rečima koje sadrže osnovnu reč.
- Analizom više različitih rečnika od preko 200,000 reči u konfiguracioni fajl je dodato 60-tak osnova reči kod kojih nije potrebno prebacivanje dva slova latinice u jedno slovo ćirilice (Npr."Nadzor" => "Надзор", a ne "Наџор".)

Pogledajte uputstvo za korišćenje biblioteke-a koje je krajnje jednostavno. Biblioteku možete implementirati u već postojeće projekte uz minimalne izmene. Na kraju dokumentacije se nalazi spisak opcija koje možete proslediti pri pozivu biblioteke, link za live demo koji će izvršiti transliteraciju ove stranice, kao i link za download.
 
## Upotreba


```html
/* index.html file */
<html>
<head>
<meta charset="utf-8">
<script src="cyrlatconverter.min.js"></script>
</head>
<body>

<a href="#" class="cyr">ćirilica</a>
<a href="#" class="lat">latinica</a>

... neki sadrzaj ...

<script>
        var CyrLat = new CyrLatConverter('body').init({
            onClickCyr: '.cyr',
            onClickLat: '.lat'
        });
    </script>
</body>
</html>
```

## Alternativni poziv plugin-a


```html
/* index.html file */
<html>
<head>
<meta charset="utf-8">
<script src="cyrlatconverter.min.js"></script>
</head>
<body>

<a href="#" id="cyr">ćirilica</a>
<a href="#" id="lat">latinica</a>

... neki sadrzaj ...

<script>
    var CyrLat = new CyrLatConverter('body').init();
    
    document.getElementById('cyr').addEventListener("click", function(){ 
        CyrLat.L2C();
    });
    
    document.getElementById('lat').addEventListener("click", function(){ 
        CyrLat.C2L();
    });
</script>
</body>
</html>
```

Možete koristiti i jQuery za <span class="CyrLatIgnore">click event</span>, biblioteka je nezavisna od njega.


## Izostavljanje odredjenih reči iz preslovljavanja

Neke od reči ne bi trebalo da budu transliterovane, kao na primer neke engleske reči koje se često koriste u srpskom jeziku, ili reči koje se često koriste na strani koja se transliteruje. Primer nekih od tih reči su "plugin", "jQuery", ili čak i "cool", "default" itd. Postoji više načina da se ovakve reči izostave iz transliteracije. Prvi je da se željena reč, ili deo teksta, obeleži "CyrLatIgnore" klasom. Ovaj način će izostaviti sve reči koje se nalaze unutar taga obeleženog "CyrLatIgnore" klasom, ali ne i njegove "child" tagove. 

```html
<div>
    Ovo je primer gde se <span class="CyrLatIgnore">English word</span> neće preslovljavati, a ostali deo teksta hoće.
</div>
```

Ukoliko želimo da izostavimo i "child" elemente iz preslovljavanja umesto "CyrLatIgnore" klase možemo koristiti bilo koju klasu, a pri inicijalizaciji plugin-a kao parametar "ignoreClasses" postavićemo klasu za koju želimo da ignoriše transliteraciju. Moguće je proslediti više klasa za ignorisanje. Primer:

```html
<div>
Ovo je primer gde se
    <span class="Moja-Klasa-Za-Ignorisanje">
        <span>child 1</span>
        <div>child 2</div>
            <span>child 1 of child 2</span>
        </div>
    </span>
neće preslovljavati, a ostali deo teksta hoće.
</div>
<script>
    var CyrLat = new CyrLatConverter('body').init({
        ignoreClasses: ['Moja-Klasa-Za-Ignorisanje']
    });
</script>
```

Poslednji, i možda i najpraktičniji način za izostavljanje reči iz transliteracije je njeno definisanje u posebnom konfiguracionom fajlu. Potrebno je uneti reč koja će biti ignorisana u već postojeći spisak, kompajlirati skriptu, i to je sve. Postoji još jedna dodatna opcija, a to je podrazumevani prikaz reči za ignorisanje. Na primer ako unesemo "jquery" : "jQuery" u spisak reči za ignorisanje, ignorisaćemo transliteraciju reči "jquery" i pri tome ćemo je prikazati kao "jQuery". Ako želimo da velika ili mala slova ostanu kao što su u tekstu, unećemo "jquery" : "". Obratite pažnju da reči koje se preslovljavaju moraju biti napisane malim slovima, a u tekstu će biti ignorisane bez obzira da li su napisane velikim, malim, ili čak mešovitim slovima.
Ovde je bitno napomenuti da je nakon promena u ovom fajlu, potrebno pokrenuti `grunt` kako bi izgenerisao novu verziju plugin-a. 
Primer reči definisanih za ignorisanje transliteracije:


```js
/* cyrlatconverter_ignore_list_rs.js */
let CyrLatIgnoreList = {
    "plugin" : '',
    "jquery" : 'jQuery',
    "default" : ''
};
```

U istom konfiguracionom fajlu postoji i niz reči koje će biti izuzete iz prebacivanja reči sa dvoznakom latinice u reč koja se sastoji od jednoznačnog slova ćirilice (Npr. "Njemačka" => "Нјемачка", a ne "Њемачка"). Ovde ne postoji opcija za podrazumevani prikaz velikih ili malih slova, već će reč biti prikazana kao u originalu. Reči za ignorisanje takodje moraju biti unete malim slovima, a u stranici će biti ignorisane bez obzira na velika ili mala slova. Primer:


```js
/* cyrlatconverter_ignore_list_rs.js */
let CyrLatIgnore_doubleLetters = {
    "autodjelovi",
    "djeca",
    "njemačka"
};
```

Takodje u istom konfiguracionom fajlu možete uočiti i unapred predefinisan spisak osnova reči koje će biti izuzete iz prebacivanja reči sa dvoznakom latinice u reč koja se sastoji od jednoznačnog slova ćirilice. Razlika izmedju ovog spiska i prethodnog je u tome što će se sve reči koje u sebi sadrže neku reč iz ovog spiska biti izuzete iz spajanja dva slova u jedno, a reči iz prethodnog spiska se moraju doslovno pojaviti u tekstu. Tako da za unetu reč "nadjača" biće ignorisano spajanje dva slova u jedno i za reči "nadjačati", "nadjačali", "nadjačano", i td. Primer:

```js
/* cyrlatconverter_ignore_list_rs.js */
let CyrLatIgnore_doubleLettersBase = {
    "adjektiv",
    "budzašto",
    "vanjezič"
    ...
};
```

Kada ste promenili spisak reci u ovoj listi, potrebno je da pokrenete `grunt` koji ce minifikovati novu verziju ukljucujuci i vase izmene.


## Izvršavanje transliteracije
Pretpostavka je da je plugin inicijalizovan kao `var CyrLat = new CyrLatConverter('body').init({...})`, znači ime promenljive `CyrLat` će biti korišćeno u primerima. Da bi se izvršila transliteracija elemenata, potrebno je pozvati plugin sa jednim od sledećih parametara:

- CyrLat.L2C() - za prikaz u Ćirilici
- CyrLat.C2L() - za prikaz u Latinici
- CyrLat.Default() - za prikaz originalne stranice


Ukoliko je uključena opcija za hash tag, onda nije potrebno dodatno pozivanje biblioteke, već će se biblioteka automatski pozvati promenom hash taga. Primer:

```html
<a href="#lat">Latinica</a>
<a href="#cyr">Ćirilica</a>
```

Ukoliko želite sami da pozovete biblioteku za transliteraciju sa nekim od gornjih parametara, imajte u vidu da je potrebno pozvati biblioteku kada je "document.ready". Sledi primer celokupne inicijalizacije biblioteke-a, gde ćete najlakše videti kako se poziva.

## Poziv korisničke funkcije posle transliteracije
Ukoliko po završenoj transliteraciji želite da pozovete neku dodatnu funkciju možete proslediti željenu funkciju kao opciju parametra. Napomena: kod nekih browser-a će se korisnička funkcija pozvati dva puta.


```js
var CyrLat = new CyrLatConverter('body').init({
	onC2L: function(){
	    alert('Vas kod');
	},
	onL2C: function(){
        alert('Vas kod');
    }
}); 
```

## Primer pomoću hash taga

```html
<html>
<head>
<meta charset="utf-8">
<script src="cyrlatconverter.min.js"></script>
</head>
<body>
<a href="#cyr">Ćirilica</a>
<a href="#lat">Latinica</a>
...
Ovde ide sadržaj body taga
...
<script>
new CyrLatConverter('body').init({
  usePermalinkHash : true
});
</script>
</body>
</html>
```

## Opcije
Spisak svih opcija koje podržava "CyrLatConverter":

cookieDuration
: Trajanje cookie-a u danima. Ukoliko se ne navede, podrazumevana vrednost je 0. Ako nekim slučajem ne želite da se pamti poslednja transliteracija, za vrednost "cookieDuration" unesite vrednos "0".

ignoreClasses
: Podrazumevana klasa za ignorisanje preslovljavanja je "CyrLatIgnore", medjutim, ova klasa ignoriše samo prvi nivo unutar obeleženog taga, i ovo je željeno ponašanje. Ukoliko neko želi da nekom klasom ignoriše i sve "child" elemente unutar obeleženog taga može to uraditi podešavanjem opcije "ignoreClasses". Potrebno je navesti klase koje će se tretirati kao klase za ignorisanje u nizu. Npr. ignoreClasses: ['prettyprint', 'ignore']. Kao što vidite navedeni primer je koristan ukoliko koristite google prettify za prikaz koda, i ovde navodimo da se google prettify klase ignorišu iz preslovljavanja. Podrazumevana vrednost je prazan niz.

usePermalinkHash
: Moguće vrednosti su `true` i `false`. Omogućava izbor da li će se transliteracija pozivati postavljanjem hash taga, ili ne. Podrazumevana vrednost je `false`.

permalinkHashLat
: Definiše tag koji će pokrenuti transliteraciju iz Ćirilice u Latinicu. Podrazumevana vrednost je "#lat".

permalinkHashCyr
: Definiše tag koji će pokrenuti transliteraciju iz Latinice u Ćirilicu. Podrazumevana vrednost je "#cyr".

ignoreListIncludeUnicode
: Ukoliko lista reči za ignorisanje u posebnom konfiguracionom fajlu sadrži i ćirilične reči ostavite ovo podešavanje kao `true`, u suprotnom možete ga podesiti na `false` čime ćete dobiti malo ubrzanje pri izvršavanju transliteracije. Podrazumevana vrednost je `true`.

benchmark
: Ukoliko je postavljeno na `true` meriće vreme izvršenja transliteracije. Pri korišćenju plugin-a sa pozivom "default" benchmark će se pozvati dva puta, poslednja vrednost je vrednost koju razmatramo. Podrazumevana vrednost je `false`. Napomena: Obratite pažnju da ako je benchmark postavljen na `true`, biće korišćena "eval" funkcija. Najbolje je koristiti benchmark samo u razvoju, a ne i u produkcionoj verziji.

benchmarkEval
: Poziv javascript eval funkcije za prikaz vrednosti benchmark-a. Vrednost "%s%" će biti zamenjena sa vremenom izvršavanja transliteracije. Podrazumevana vrednost je "console.log('Execution time: %s%')".

onClickCyr
: Selektor elementa, koje kada kliknemo pozivamo transliteraciju iz latinice u ćirilicu.

onClickLat
: Selektor elementa, koje kada kliknemo pozivamo transliteraciju iz ćirilice u latinicu.

onClickDefault
: Selektor elementa, koje kada kliknemo prikazujemo originalni sadržaj stranice.

onC2L
: Funkcija koja će se pozvati po završenoj transliteraciji C2L. Default: undefined.

onL2C
: Funkcija koja će se pozvati po završenoj transliteraciji L2C. Default: undefined.


Ovde možete <a href="https://github.com/ivebe/CyrLatConverter/archive/master.zip">**skinuti ( DOWNLOAD )**</a> najnoviji kod, a ukoliko vam se svidela biblioteka ne zaboravite da lajkujete/podelite ovaj blog sa drugima kako bi ga i drugi koristili. Ukoliko primetite neki bug, prijavite ga u komentarima, ili na <a href="https://github.com/ivebe/CyrLatConverter">GitHub</a> stranici.

<ul class="blog-info">
<li><i class="fa fa-user"></i> By Dan</li>
<li><i class="fa fa-calendar"></i> {{ page.date | date: '%B %d, %Y' }}</li>
<li><i class="fa fa-tags"></i> {{ page.categories }}</li>
</ul>

{% if page.comments %}
{% include comments.md %}
{% endif %}
