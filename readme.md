# Introduktion til Testning af Asynkrone Funktioner og Fetch

## Asynkrone Funktioner
Asynkrone funktioner i JavaScript gør det muligt at udføre opgaver, der tager tid, som f.eks. at hente data fra et API, uden at blokere resten af din kode. Du kan oprette en asynkron funktion ved at bruge `async` nøgleordet og håndtere asynkrone operationer med `await` nøgleordet.

Eksempel:
```javascript
async function fetchData() {
  const response = await fetch('https://api.example.com/data');
  const data = await response.json();
  return data;
}
```

## Testning af Asynkrone Funktioner med Jest

Når du tester asynkrone funktioner, skal du fortælle Jest at vente på, at de asynkrone operationer er færdige. Dette kan du gøre ved at returnere et løfte eller bruge `async`/`await` syntaksen i dine tests. Ved at bruge `global.fetch = jest.fn();` gør vi følgende:

1. **Mocking af `fetch`-funktionen:**
   - `jest.fn()` opretter en mock-funktion, som vi kan bruge til at simulere `fetch`-kald. Denne mock-funktion kan konfigureres til at returnere specifikke værdier eller opføre sig på bestemte måder, hvilket gør det muligt at teste forskellige scenarier uden at lave rigtige netværksanmodninger.

2. **Globalt scope:**
   - Ved at tildele mock-funktionen til `global.fetch` sikrer vi, at alle `fetch`-kald i vores tests bruger denne mock-funktion i stedet for den rigtige `fetch`-funktion. Dette er nyttigt, fordi `fetch` er en global funktion i browsermiljøer.

#### Eksempel på Brug

Når vi mocker `fetch`, kan vi konfigurere mock-funktionen til at returnere en succesfuld respons med de specificerede data, når `fetch` kaldes. Dette gør det muligt at teste funktionaliteten af vores kode uden at være afhængige af eksterne API'er, hvilket gør vores tests hurtigere og mere pålidelige.

Ved at mocke `fetch`-funktionen kan vi teste vores kode uden at lave rigtige netværksanmodninger. Dette gør vores tests mere pålidelige og hurtigere, da de ikke er afhængige af eksterne faktorer som netværksforbindelse og API-tilgængelighed.

```javascript
const fetchData = require('./fetchExample');

// Mock den globale fetch-funktion
global.fetch = jest.fn();

test('should fetch data successfully', async () => {
  const mockResponse = { data: 'some data' };
  global.fetch.mockResolvedValue({
    ok: true,
    json: async () => mockResponse,
  });

  const data = await fetchData('https://api.example.com/data');
  expect(data).toEqual(mockResponse);
});

test('should throw an error for a failed fetch', async () => {
  global.fetch.mockResolvedValue({
    ok: false,
  });

  await expect(fetchData('https://api.example.com/data')).rejects.toThrow('Network response was not ok');
});
```

### Forklaring

#### Mocking fetch:
`global.fetch = jest.fn();` opretter en mock-funktion for `fetch`.

#### Konfiguration af Mock-respons:
`global.fetch.mockResolvedValue({ ok: true, json: async () => mockResponse });` konfigurerer mock-funktionen til at returnere en succesfuld respons med de specificerede data, når `fetch` kaldes.

`global.fetch.mockResolvedValue({ ok: false });` konfigurerer mock-funktionen til at simulere en mislykket netværksanmodning.

#### Test af Funktionalitet:
I den første test kontrollerer vi, at dataene hentes korrekt, når `fetch`-anmodningen lykkes.

I den anden test kontrollerer vi, at der kastes en fejl, når `fetch`-anmodningen mislykkes.




# Opgave: Pokémon Info med Unit Tests

Opret en Pokémon Info App ved hjælp af JavaScript, der henter data fra PokeAPI, og skrive unit tests for den ved hjælp af Jest.

## Krav
Applikationen skal kunne hente Pokémon-data ved navn eller ID fra [PokeAPI](https://pokeapi.co/).

Koden skal være organiseret i moduler, for eksempel `pokemon-service.js` til API-logikken. Det er vigtigt, at koden er modulær, så hver funktion kan testes.

## Instruktioner
Start med at oprette et nyt projekt ved hjælp af `npm init` og installer Jest med `npm install jest`. Implementer derefter applikationen ved at oprette en `pokemon-service.js` fil, der indeholder den grundlæggende logik til at hente Pokémon-data. 

Når implementeringen er på plads, skal du skrive unit tests for hver funktion i `pokemon-service.js`. Opret en `pokemon-service.test.js` fil og skriv tests, der dækker funktionerne til at hente Pokémon-data, parse og vise data samt håndtere fejl, såsom ugyldige Pokémon-navne eller ID'er og netværksproblemer. Brug Jest til at mocke API-kald for at undgå at ramme det faktiske API under tests.

Til sidst skal du commit'e din kode sammen med testresultaterne.

### Eksempel på funktion/metode

```javascript
static baseUrl = 'https://pokeapi.co/api/v2/pokemon';

  static async getPokemon(nameOrId) {
    const response = await fetch(`${PokemonService.baseUrl}/${nameOrId}`);
    if (!response.ok) {
      throw new Error('Pokémon not found');
    }
    const data = await response.json();
    return {
      name: data.name,
      types: data.types.map(typeInfo => typeInfo.type.name),
      image: data.sprites.front_default,
    };
  }
```


