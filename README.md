# Opgaveskabelon til "Figma til kode"

Se opgavebeskrivelsen på ItsLearning.

## Refleksion

Skriv refleksionen i [REFLEKSION.md](./REFLEKSION.md).

Refleksionen skal ikke skrives i README.

## Data i opgaven

I opgaven skal du som udgangspunkt hente data fra API'et:

https://ftk-api.pages.dev

API'et simulerer et eksternt data-endpoint, så du kan øve `fetch()`.

Eksempel:

```
const response = await fetch("https://ftk-api.pages.dev/team");
const team = await response.json();
```

Du kan se de tilgængelige endpoints og eksempler her:

https://ftk-api.pages.dev/endpoints

## Sider i skabelonen

- `src/pages/index.astro` → `/`: Forsiden med en påbegyndt Hero-komponent.
- `src/pages/about.astro` → `/about`: Fælles Layout og en overskrift.
- `src/pages/team/index.astro` → `/team`: Fælles Layout og en overskrift.
- `src/pages/team/[slug].astro` → `/team/[slug]`: Data og grundindhold for den enkelte medarbejder.
- `src/pages/case-studies/[slug].astro` → `/case-studies/[slug]`: Data og markup til caseartiklen.

Siderne bruger `src/layouts/Layout.astro`. I projektet er navigation, indhold, komponenter og styling bygget videre på den udleverede skabelon.

## API-data i komponenter

Data hentes fra API'et med `fetch()` og bruges i Astro-komponenterne.

Eksempelvis hentes teamdata fra `/team`, hvorefter dataene bruges til at oprette de enkelte medarbejderkort og medarbejdersider.

I det statiske Astro-projekt hentes data ved build og under udvikling på dev-serveren. Dataene bliver derfor ikke hentet med JavaScript i brugerens browser.

Case Study- og Team Member-siderne bruger dynamiske routes med `getStaticPaths()`.

Astro bruger file-based routing, hvor filer i `src/pages/` danner routes ud fra deres placering og filnavn. Dynamiske routes som `[slug].astro` kan generere flere sider ud fra data ved build. [Astro – Routing](https://docs.astro.build/en/guides/routing/)

## Billeder fra API'et

Billeder fra API'et returneres som billeddata:

```
{
  "image": {
    "src": "https://ftk-api.pages.dev/images/sarah.webp",
    "alt": "Sarah Jasmine",
    "width": 732,
    "height": 784
  }
}
```

Skabelonen er sat op til at kunne bruge billeder fra `ftk-api.pages.dev` med Astros `Image`-komponent.

```
---
import { Image } from "astro:assets";
---

<Image
  src={employee.image.src}
  alt={employee.image.alt}
  width={employee.image.width}
  height={employee.image.height}
/>
```

> [!NOTE]
> Bemærk, at Case Study-siden allerede var sat op i skabelonen.
>
> Bemærk også, at ikke alle billeder fra Figma-filen findes i API'et.

## Brug af hjælpekomponenter

### DynamicIcon.astro (`@helpers/DynamicIcon.astro`)

`DynamicIcon` bruges til at vise SVG-ikoner ud fra et ikonnavn fra data.

API'et returnerer fx:

```
{
  "platform": "instagram",
  "icon": "instagram"
}
```

Det matcher en lokal SVG-fil i `src/icons/`.

Eksempel:

```
---
import DynamicIcon from "@helpers/DynamicIcon.astro";
---

{employee.social_links.map((link) => (
  <a href={link.url} aria-label={link.platform}>
    <DynamicIcon
      name={link.icon}
      width={24}
      height={24}
      class="social-icon"
    />
  </a>
))}
```

Hvis ikonet ikke findes, vises der ikke noget output, og komponenten logger en advarsel i konsollen.

## Links fra data

Nogle datafelter indeholder linkdata, fx:

```
{
  "link": {
    "text": "Read More",
    "url": "#"
  }
}
```

De kan bruges sådan:

```
<a href={item.link.url}>{item.link.text}</a>
```

Hvis et link kun indeholder et ikon og ingen synlig tekst, skal linket have et tilgængeligt navn, fx med `aria-label`.

```
<a href={link.url} aria-label={link.platform}>
  <DynamicIcon name={link.icon} />
</a>
```

## Import af SVG-ikoner direkte

SVG-ikoner kan også importeres direkte i komponenterne:

```
---
import Checkmark from "@icons/checkmark.svg";
---

<Checkmark width={32} height={32} class="my-icon" />
```

Se evt. `src/pages/svgs.astro` for flere eksempler på direkte import og brug af SVG-ikoner.

---

## Lokal backup-data

Der ligger også lokale JSON-filer i `src/data/`. De kan bruges som backup, hvis API'et ikke virker, eller hvis du vil teste uden netværkskald.

Dokumentation til lokal data findes her:

https://ftk-api.pages.dev/local.html

Bemærk, at lokal data ikke nødvendigvis har præcis samme struktur som API-svarene.

### DynamicImage.astro (`@helpers/DynamicImage.astro`)

`DynamicImage` er kun relevant, hvis du arbejder med lokale billeder fra `src/data/images/`.

Du skal som udgangspunkt ikke bruge `DynamicImage` til billeder fra API'et, fordi API'et allerede returnerer offentlige billed-URL'er og dimensioner. Brug i stedet Astros `Image`-komponent som vist ovenfor.

---

# Den færdige løsning

Projektet er udviklet som en responsiv AskExperts-website med udgangspunkt i det udleverede Figma-design.

## Sider

Projektet indeholder:

- `/` – Forside
- `/about` – About
- `/team` – Team
- `/team/[slug]` – Individuel medarbejder
- `/case-studies/taxes-and-efficiency` – Case Study

Derudover indeholder projektet de routes og hjælpefiler, som følger med den udleverede skabelon.

## Teknologier

Projektet er udviklet med:

- Astro
- HTML
- CSS
- REST API
- Git og GitHub
- Netlify

Der er blandt andet arbejdet med:

- responsive layouts
- CSS custom properties
- design tokens
- CSS Grid
- container queries
- `--flow-space`
- `<details>` og `<summary>`
- Popover API
- CSS Anchor Positioning
- progressive enhancement
- genanvendelige Astro-komponenter

## Deployment

Den færdige løsning er deployet til Netlify:

[Åbn den færdige AskExperts-side](https://temaopgave-figma-til-kode-askexperts.netlify.app/)

## Build

Produktionsbuilden testes med:

```
npm run build
```

Builden skal gennemføre uden fejl, før projektet afleveres.

## Dokumentation

De faglige refleksioner over de valgte teknikker, test, ændringer undervejs og brug af AI findes i [REFLEKSION.md](./REFLEKSION.md).
