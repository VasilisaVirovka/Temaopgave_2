# Refleksion – Figma til kode

**Gruppemedlemmer:** Vasilisa Virovka

## Eksempel 1: Subgrid

### Hvor og hvorfor?

Jeg har arbejdet med CSS `subgrid` på Case Study-siden. Formålet var at skabe en sammenhængende gridstruktur, hvor forskellige dele af siden kan følge den samme overordnede kolonneopdeling.

Jeg arbejdede især videre med dette den 4. oktober, hvor jeg havde fokus på at inkorporere og forbedre brugen af `subgrid`. Det var vigtigt for mig, at `subgrid` ikke kun blev anvendt, fordi det var et krav, men fordi det løste et konkret layoutproblem.

### Relevant kode

I `src/pages/case-studies/[slug].astro` bruger jeg blandt andet:

    .case-story-header,
    .case-story-content {
      display: grid;
      grid-template-columns: subgrid;
      grid-column: 2 / -2;
      min-inline-size: 0;
    }

På den måde kan header og indhold følge den samme overordnede gridstruktur.

### Afprøvning og ændringer

**Jeg testede:** Case Study-layoutet på forskellige skærmstørrelser.

**Jeg observerede:** At gridstrukturen skulle justeres, så indholdet fortsat var overskueligt på mindre skærme.

**Jeg ændrede:** Jeg arbejdede videre med responsive breakpoints og tilpassede gridstrukturen, så `subgrid` bruges på desktop, mens layoutet forenkles på mindre skærme.

## Eksempel 2: Container queries og responsive layout

### Hvor og hvorfor?

Jeg har anvendt container queries for at gøre komponenter mere responsive. I stedet for kun at reagere på hele viewportens bredde kan en komponent reagere på størrelsen af den container, den befinder sig i.

Det er blandt andet anvendt i Header-komponenten, hvor navigationen ændrer struktur, når headerens container bliver mindre.

### Relevant kode

    .site-header {
      container-type: inline-size;
    }

    @container (max-width: 700px) {
      .site-nav {
        grid-template-columns: 1fr auto;
        gap: var(--space-4);
      }

      .nav-links {
        grid-column: 1 / -1;
        grid-row: 2;
        justify-content: center;
        gap: var(--space-4);
      }
    }

### Afprøvning og ændringer

**Jeg testede:** Navigationen ved forskellige skærmstørrelser.

**Jeg observerede:** At navigationen skulle kunne tilpasse sig, når der blev mindre plads.

**Jeg ændrede:** Layoutet, så navigationen ændrer placering ved mindre containerbredder frem for blot at gøre elementerne mindre.

## Eksempel 3: Browserkompatibilitet og progressive enhancement

### Hvor og hvorfor?

Et tredje fokusområde har været progressive enhancement og browserkompatibilitet. Jeg har arbejdet med moderne browserfunktioner, men samtidig undersøgt, hvad der sker, hvis funktionerne ikke fungerer ens i forskellige browsere.

Dette blev især mit fokus den 5. oktober, hvor jeg arbejdede med browserkompatibilitet i Chrome, Safari og Firefox.

Et konkret problem var Login Popoveren i Safari. Jeg anvender Popover API, men popoveren opførte sig ikke korrekt i Safari. Derfor arbejdede jeg med en mulig JavaScript-baseret fallback.

### Relevant kode – Login Popover

Den moderne løsning anvender blandt andet:

    <button
      class="login-button"
      type="button"
      popovertarget="login-popover"
    >
      Login
    </button>

    <div id="login-popover" class="login-popover" popover>
      ...
    </div>

Jeg har undersøgt en fallback, hvor browserens understøttelse kontrolleres:

    const supportsPopover = "popover" in HTMLElement.prototype;

Hvis browseren ikke understøtter Popover API, kan JavaScript i stedet håndtere åbning og lukning:

    if (!supportsPopover) {
      loginPopover.classList.add("popover-fallback");
      loginButton.removeAttribute("popovertarget");

      loginButton.addEventListener("click", () => {
        loginPopover.classList.toggle("is-open");
      });
    }

### Afprøvning og ændringer

**Jeg testede:** Login Popoveren i forskellige browsere, blandt andet Safari.

**Jeg observerede:** At popoveren ikke opførte sig korrekt i Safari.

**Jeg ændrede:** Jeg arbejdede med en JavaScript-baseret fallback, så den moderne Popover API kan anvendes, når den fungerer, mens JavaScript kan overtage funktionen, hvis API'en ikke understøttes.

Jeg har dog en begrænsning i forhold til testen, fordi jeg udvikler på en ældre MacBook med et ældre macOS-system. Jeg kan derfor ikke lokalt teste alle kombinationer af nyere Safari-versioner og andre styresystemer.

Efter deployment til Netlify kan jeg teste løsningen på nyere Apple-enheder, eksempelvis en iPhone 16 eller en iPad fra 2024. Det er derfor vigtigt at skelne mellem en løsning, som er implementeret, og en løsning, som er fuldt verificeret på alle relevante platforme.

## Fallback og robusthed

### Doughnut chart og Firefox

Doughnut chartet har været en anden browserkompatibilitetsudfordring. Den oprindelige løsning anvender SVG og CSS, blandt andet `offset-path` til at placere markøren rundt på cirklen.

Eksempel:

    .marker {
      offset-path: circle(46px at 50px 50px);
      offset-distance: calc(var(--value-number) * 1%);
    }

Problemet er, at doughnut chartet ikke fungerer visuelt korrekt i Firefox på samme måde som i Chrome.

Min foreslåede løsning er at bruge feature detection:

    const supportsOffsetPath = CSS.supports(
      "offset-path: circle(46px at 50px 50px)"
    );

Hvis funktionen ikke fungerer, kan markørens placering i stedet beregnes med JavaScript:

    const angle = (value / 100) * 360 - 90;
    const radians = angle * Math.PI / 180;

    const x = 50 + 46 * Math.cos(radians);
    const y = 50 + 46 * Math.sin(radians);

    marker.setAttribute("cx", x);
    marker.setAttribute("cy", y);

På den måde kan den eksisterende løsning bevares i Chrome, mens JavaScript kan bruges som fallback i browsere, hvor `offset-path` ikke giver det forventede resultat.

Dette er en løsning, jeg vil arbejde videre med og teste efter deployment. Jeg vil derfor ikke beskrive problemet som endeligt løst, før fallbacken er verificeret i de relevante browsere.

### Relative Color Syntax

Den 5. oktober implementerede jeg Relative Color Syntax som mit ekstra moderne CSS-element.

Jeg bruger den eksisterende design-token `--color-action` og ændrer lightness-værdien ved hover og focus:

    .logo:hover span:last-child,
    .logo:focus-visible span:last-child {
      color: hsl(
        from var(--color-action)
        h
        s
        calc(l - 10%)
      );
    }

Jeg har samtidig lavet en fallback:

    @supports not (color: hsl(from red h s l)) {
      .logo:hover span:last-child,
      .logo:focus-visible span:last-child {
        color: var(--color-action);
      }
    }

Hvis browseren ikke understøtter Relative Color Syntax, anvendes den eksisterende farve i stedet. Det betyder, at funktionen fungerer som progressive enhancement og ikke er nødvendig for, at navigationen fungerer.

### Defensive CSS

Jeg har blandt andet arbejdet med `min-inline-size: 0` i grid-layoutet:

    .case-story-header,
    .case-story-content {
      min-inline-size: 0;
    }

Det er med til at forhindre, at grid-items skubber layoutet ud af deres container, hvis indholdet kræver mere plads.

### Global CSS og komponent-CSS

Jeg har samlet fælles designværdier i `tokens.css`, blandt andet farver, spacing, typografi og border-radius. Komponent-specifik styling ligger i de relevante komponenter, så styling, der kun vedrører en bestemt komponent, ikke unødigt bliver gjort global.

## Brug af AI

Jeg har brugt AI som sparringspartner under udviklingen. Det har blandt andet været til hjælp med at forstå og fejlfinde `subgrid`, container queries, browserkompatibilitet, Popover API, doughnut chartet og progressive enhancement.

Jeg har ikke ukritisk implementeret alle forslag. Jeg har sammenholdt forslagene med min egen kode og testet funktionerne i browsernes DevTools. Et konkret eksempel er doughnut chartet, hvor jeg først undersøgte problemet i Firefox og derefter arbejdede med en fallback, som bevarer den eksisterende visuelle løsning i Chrome.

AI har derfor primært fungeret som sparring og hjælp til fejlfinding, mens jeg selv har vurderet, ændret og implementeret løsningerne i projektet.

# Procesnoter

## 25. september 2026 – Projektopsætning og GitHub

Jeg downloadede opgavens projekt og connected det til mit GitHub-repository. Det blev udgangspunktet for versionsstyring og den videre udvikling af projektet.

## 28. september 2026 – Grundstruktur

Jeg startede med at etablere den fælles struktur for websitet. Header, footer og login-knap blev oprettet som genanvendelige komponenter og koblet på det fælles `Layout.astro`.

Jeg udvidede samtidig `tokens.css` med genbrugelige værdier for spacing, typografi og border-radius, så værdierne kan genbruges på tværs af komponenterne i stedet for at definere dem individuelt.

Jeg committede denne milepæl til GitHub, så udviklingen af projektet kan følges løbende.

## 29. september 2026 – Home

Jeg arbejdede videre med Home-siden og dens komponenter, blandt andet Hero, Services og de øvrige sektioner.

Jeg arbejdede samtidig med doughnut chartet, som senere viste sig at være en browserkompatibilitetsudfordring i Firefox.

## 30. september 2026 – About

Jeg arbejdede med About-siden og tilpassede layoutet til Figma-designet. Samtidig fortsatte jeg med at sikre, at siden fungerede responsivt.

## 1.–2. oktober 2026 – Team

Jeg arbejdede med Team-siden og de dynamiske team-profiler. Her arbejdede jeg blandt andet med Astro-routing og dynamisk generering af profiler ud fra data.

## 3. oktober 2026 – Case Study og deployment

Jeg arbejdede videre med Case Study-siden og dens struktur. Jeg anvendte semantiske HTML-elementer som `article`, `header`, `section`, `dl`, `dt`, `dd`, `figure` og `figcaption`.

Jeg arbejdede også med `--flow-space` til spacing mellem sektionerne og med gridstrukturen på Case Study-siden.

Projektet blev desuden deployet til Netlify, så løsningen kunne testes uden for det lokale udviklingsmiljø.

## 4. oktober 2026 – Subgrid

Jeg havde specifikt fokus på at inkorporere og forbedre `subgrid` på Case Study-siden.

Jeg arbejdede med at få de forskellige dele af Case Study-layoutet til at følge den samme overordnede gridstruktur og tilpassede samtidig layoutet til mindre skærme.

## 5. oktober 2026 – Browserkompatibilitet og Relative Color Syntax

Jeg havde primært fokus på browserkompatibilitet.

Jeg undersøgte blandt andet problemet med Login Popoveren i Safari og arbejdede med en JavaScript-baseret fallback. Jeg arbejdede også videre med browserproblemet omkring doughnut chartet i Firefox og en mulig fallback baseret på feature detection og beregning af markørens position.

Derudover implementerede jeg Relative Color Syntax i Header-komponenten og lavede en `@supports`-fallback.

Arbejdet denne dag gjorde det tydeligt for mig, at browserkompatibilitet ikke kun handler om selve koden, men også om hvilke browsere og styresystemer der er tilgængelige til test.
