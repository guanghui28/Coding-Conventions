# CODING CONVENTIONS
### 1. Avoid variables with a single letter

**DON'T**
``` ts
public parseCookie(n: string): string | null {
    const s = `; ${document.cookie}`.split('; ');
    for (let c of s) {
        if (c.indexOf(n) === 0) {
            return c.substring(c.length + 1);
        }
    }

    return null;
}
```
**DO**
``` ts
public parseCookie(name: string): string | null {
    const cookieStrs = `; ${document.cookie}`.split('; ');
    for (let cookieStr of cookieStrs) {
        if (cookieStr.indexOf(name) === 0) {
            return cookieStr.substring(cookieStr.length + 1);
        }
    }

    return null;
}
```

### 2. Never Abbreviate

**DON'T**

```ts
public static relScore(m1: Mov, m2: Mov): number {
    const GW = 0.4;
    const YW = 0.1;
    const DW = 0.2;
    const WW = 0.3;

    let s = 0;
    if (m1.gen === m2.gen) {
        s += GW;
    }

    if (m1.yr === m2.yr) {
        s += YW;
    }

    if (m1.dir === m2.dir) {
        s += DW;
    }

    for (const w in m1.writers) {
        if (m2.writers.indexOf(w) !== -1) {
            s += WW;
            break;
        }
    }

    return s;
}
```

**DO**

```ts
public static movieRelationScore(movie1: Movie, movie2: Movie): number {
    const GENRE_WEIGHT = 0.4;
    const YEAR_WEIGHT = 0.1;
    const DIRECTOR_WEIGHT = 0.2;
    const WRITER_WEIGHT = 0.3;

    let score = 0;
    if (movie1.genre === movie2.genre) {
        score += GENRE_WEIGHT;
    }

    if (movie1.year === movie2.year) {
        score += YEAR_WEIGHT;
    }

    if (movie1.director === movie2.director) {
        score += DIRECTOR_WEIGHT;
    }

    for (const writer in movie1.writers) {
        if (movie2.writers.indexOf(writer) !== -1) {
            score += WRITER_WEIGHT;
            break;
        }
    }

    return score;
}
```
### 3. Don't put types in your names

**DON'T**

```ts
let bIsValid: Boolean;
let nSpeed: Number;
let sName: String;
```

**DO**

```ts
let isValid: Boolean;
let speed: Number;
let name: String;
```

### 4. Add units to variables unless the type tells you

**DON'T**

```ts
public execute(delay: number): void {
    // Do something here
}
```

**DO**

```ts
public execute(delaySeconds: number): void {
    // Do something here
}
```

**BETTER**
```ts
public execute(delay: TimeSpan): void {
    const seconds = delay.totalSeconds;
    // Do something here
}
```

### 5. Avoid adding Types in your types
> *"Good codes use interfaces all the time. Not just care about Interface, Abstract Class or Class, or Types"*

**DON'T**

```ts
interface IMovable {
    getCurrentPosition(): Vector3;
}
```

**DO**

```ts
interface Movable {
    getCurrentPosition(): Vector3;
}

class PositionAnimation {
    private movable: Movable;
    private start: Vector3;
    private target: Vector3;

    constructor(movable: Movable, target: Vector3) {
        this.movable = movable;
        this.start = movable.getCurrentPosition();
        this.target = target;
    }
    // other logic
}
```

### 6. Avoid naming a class "Base" Or "Abstract"

**DON'T**

```ts
class Truck extends BaseTruck {
    // implements logic
}
```

**DO**

```ts
class TrailerTruck extends Truck {
    // implements logic
}

class Race {
    // they don't care what type of truck is
    private contestants: Truck[] = [];

    public addContestant(truck: Truck): void {
        this.contestants.push(truck);
    }
}

class ShipmentManager {
    // if they care the specific truck instead
    public schedule(truck: TrailerTruck): void {
        // do something here
    }
}
```

### 7. Refactor if you find yourself naming code "Utils"

**DON'T**

```ts
import { Movie } from './movies';

export class Utils {
    public static assignDefaults(movie: Movie): void {
        let assignIfNeeded = <Key extends keyof Movie, Val extends Movie[Key]>(key: Key, value: Val) => {
            if (!movie[key]) {
                movie[key] = value;
            }
        }

        assignIfNeeded('title', 'Unannounced Production');
        assignIfNeeded('rating', 7);
        assignIfNeeded('genre', 'Unknown');
        assignIfNeeded('year', new Date().getFullYear());
    }

    public static relationScore(movie1: Movie, movie2: Movie): number {
        const GENRE_WEIGHT = 0.4;
        const YEAR_WEIGHT = 0.1;
        const DIRECTOR_WEIGHT = 0.2;
        const WRITER_WEIGHT = 0.3;

        let score = 0;
        if (movie1.genre === movie2.genre) {
            score += GENRE_WEIGHT;
        }

        if (movie1.year === movie2.year) {
            score += YEAR_WEIGHT;
        }

        if (movie1.director === movie2.director) {
            score += DIRECTOR_WEIGHT;
        }

        for (const writer in movie1.writers) {
            if (movie2.writers.indexOf(writer) !== -1) {
                score += WRITER_WEIGHT;
                break;
            }
        }

        return score;
    }

    public static topMoviesByRating(movies: Movie[], numTop: number): Movie[] {
        return [...movies].sort((movie1, movie2) => movie1.rating - movie2.rating).splice(0, numTop);
    }

    public static moviesByDirector(movies: Movie[], director: string): Movie[] {
        return movies.filter(movie => movie.director === director)
    }

    public static moviesOnPage(movies: Movie[], page: number): Movie[] {
        const amountPerPage = 12;
        const start = (page - 1) * amountPerPage;
        return movies.slice(start, start + page);
    }

    public static parseCookie(name: string): string | null {
        const cookieStrs = `; ${document.cookie}`.split('; ');
        for (let cookieStr of cookieStrs) {
            if (cookieStr.indexOf(name) === 0) {
                return cookieStr.substring(cookieStr.length + 1);
            }
        }

        return null;
    }

    public static assignCookie(name: string, value: string, expires: Date): void {
        document.cookie = `${name}=${value}; expires:${expires.toUTCString()}; path=/`;
    }
}
```

**DO**

```ts
// ./movie.ts
export class Movie {
    // omits...
    public assignDefaults(): void {
        let assignIfNeeded = <Key extends keyof this, Val extends this[Key]>(key: Key, value: Val) => {
            if (!this[key]) {
                this[key] = value;
            }
        }

        assignIfNeeded('title', 'Unannounced Production');
        assignIfNeeded('rating', 7);
        assignIfNeeded('genre', 'Unknown');
        assignIfNeeded('year', new Date().getFullYear());
    }

    public relationScore(other: Movie): number {
        const GENRE_WEIGHT = 0.4;
        const YEAR_WEIGHT = 0.1;
        const DIRECTOR_WEIGHT = 0.2;
        const WRITER_WEIGHT = 0.3;

        let score = 0;
        if (this.genre === other.genre) {
            score += GENRE_WEIGHT;
        }

        if (this.year === other.year) {
            score += YEAR_WEIGHT;
        }

        if (this.director === other.director) {
            score += DIRECTOR_WEIGHT;
        }

        for (const writer in this.writers) {
            if (other.writers.indexOf(writer) !== -1) {
                score += WRITER_WEIGHT;
                break;
            }
        }

        return score;
    }
}
```

```ts
// ./movie-collection.ts
export class MovieCollection {
    private readonly movies: Movie[];
    private readonly moviesByDirector = new Map<String, Movie[]>();

    constructor(movies: Movie[]) {
        this.movies = movies;

        for (let movie of movies) {
            if (!this.moviesByDirector.get(movie.director)) {
                this.moviesByDirector[movie.director] = [movie];
            } else {
                this.movies[movie.director].push(movie)
            }
        }
    }

    public topByRating(numTop: number): Movie[] {
        return [...this.movies].sort((movie1, movie2) => movie1.rating - movie2.rating).splice(0, numTop);
    }

    public byDirector(director: string): Movie[] {
        return this.moviesByDirector.get(director);
    }
}
```

```ts
// ./pager.ts
export class Pager<T> {
    private readonly perPage: number;
    private readonly entities: T[];

    constructor(perPage: number, entities: T[]) {
        this.perPage = perPage;
        this.entities = entities;
    }

    public static moviesOnPage(page: number): T[] {
        const start = (page - 1) * this.perPage;
        return this.entities.slice(start, start + page);
    }
}
```

```ts
// ./cookie.ts
export class Cookie {
    public static parseCookie(name: string): string | null {
        const cookieStrs = `; ${document.cookie}`.split('; ');
        for (let cookieStr of cookieStrs) {
            if (cookieStr.indexOf(name) === 0) {
                return cookieStr.substring(cookieStr.length + 1);
            }
        }

        return null;
    }

    public static assignCookie(name: string, value: string, expires: Date): void {
        document.cookie = `${name}=${value}; expires:${expires.toUTCString()}; path=/`;
    }   
}
```