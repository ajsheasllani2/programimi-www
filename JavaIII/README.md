# Java III — Klinika e CSS: shpëto afishen

Afishja e klubit të debatit, e kthyer nga një panel i palexueshëm në një ftesë të qartë.

## Struktura

```
JavaIII/
├── index.html          # afishja (titull, datë, vend, përshkrim, lidhje regjistrimi)
├── style.css           # CSS i jashtëm: variabla, klasa të ripërdorshme, focus/hover
├── Fillimi/
│   └── gabime.css      # diagnoza dhe riparimi i dy gabimeve
└── README.md
```

## Si plotësohen kërkesat

| Kërkesa | Ku |
|---|---|
| Afishe me titull, datë, vend, përshkrim, lidhje regjistrimi | `index.html` — `<article class="afishe">`, data me `<time>`, detajet në `<dl>` |
| CSS i jashtëm, klasa të ripërdorshme, variabla | `style.css` — `:root` me `--ngjyra-*` dhe `--hapesire-*`; klasa si `.etiketa`, `.buton`, `.mbajtes` |
| Tri etiketa pa u mbështetur vetëm te ngjyra | Çdo etiketë ka tekst, ikonë dhe stil tjetër kufiri: **Falas** (vijë e plotë, ✓), **Vende të kufizuara** (vijë me ndërprerje, !, tekst i trashë), **Edhe online** (vijë e dyfishtë, ◎) |
| Diagnoza e gabimeve, pa `!important` | `Fillimi/gabime.css` — shpjegimi në koment, rregulli i riparuar poshtë tij |
| Focus i dukshëm | `:focus-visible` me kontur portokalli 3px dhe `outline-offset` (shihet me Tab te "Kalo te përmbajtja" dhe te butoni "Regjistrohu") |

## Gabimet në `Fillimi/gabime.css`

**1. Konflikti i specifikës.** `#poster` dhe `.poster` i japin vlera të ndryshme `color`. ID-ja ka specifikë (1,0,0), klasa (0,1,0), prandaj fiton `#poster` edhe pse `.poster` vjen më vonë. Rezultati ishte tekst i bardhë mbi sfond të bardhë.

**2. Tejkalimi i gjerësisë.** Me `content-box` (vlera e paracaktuar), gjerësia reale ishte 700 + 80 + 80 = **860px**, shumë më shumë se ekrani 360px.

**Riparimi:** hoqa rregullin me ID dhe lashë një rregull të vetëm me klasë (s'ka më konflikt), shtova `box-sizing: border-box`, `width: 100%` me `max-width: 700px`, dhe `padding` me `clamp()` që zvogëlohet në celular.

## width, padding, border dhe box-sizing

| | `content-box` (paracaktuar) | `border-box` |
|---|---|---|
| Çfarë mat `width` | vetëm përmbajtjen | përmbajtje + padding + border |
| `width: 700px; padding: 80px; border: 1px` | 700 + 160 + 2 = **862px** | **700px** (përmbajtja mbetet 538px) |
| Në ekran 360px | del jashtë | me `max-width: 100%` përshtatet |

Në `style.css` përdor `*, *::before, *::after { box-sizing: border-box; }`, prandaj `width: 100%` i panelit nuk e kalon kurrë prindin, sado padding të ketë.

## Si e testova

- **360px:** DevTools → Toggle device toolbar → gjerësia 360. Paneli qëndron brenda ekranit, s'ka shirit horizontal; etiketat kalojnë në rresht të ri.
- **Fokusi:** shtyp Tab. Shfaqet fillimisht "Kalo te përmbajtja", pastaj butoni "Regjistrohu" me kontur portokalli.
- **Kontrasti:** teksti `#17263c` mbi `#ffffff`; teksti i bardhë mbi butonin `#176b5c`.

## Reflektim individual

**Cili rregull fitoi në kaskadë dhe pse?**

Në skedarin origjinal fitoi `#poster`. Kaskada krahason fillimisht rëndësinë dhe origjinën (të dyja ishin të njëjta: stil autori, pa `!important`), pastaj **specifikën**, dhe vetëm në fund **renditjen**. Meqë `#poster` ka specifikë (1,0,0) dhe `.poster` vetëm (0,1,0), ID-ja fitoi para se renditja të kishte rëndësi. Kjo më tregoi pse ID-të në CSS janë të rrezikshme: ato mund të mbishkruhen vetëm me një tjetër ID ose me `!important`. Prandaj e rregullova duke ulur specifikën (vetëm klasa) në vend që ta "mund" me `!important`.

Pjesa e trashëgimisë: `color` vendoset një herë te `body` dhe e trashëgojnë të gjithë elementët brenda, ndërsa `padding`, `border` dhe `background` nuk trashëgohen.
