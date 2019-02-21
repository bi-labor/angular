# Angular bevezető

Az Angular egy TypeScript alapú keretrendszer kliens oldali webalkalmazások fejlesztéséhez. A keretrendszer elődje 2010-ben jelent meg AngularJS néven (ez még JavaScript alapú volt), majd 2016-ban került kiadásra a teljesen új alapokra helyezett 2-es verzió, amely új neve Angular.

A keretrendszer magas szintű támogatást nyújt a modern webalkalmazások fejlesztésekor általánosan felmerülő feladatokra, mint például adatkötés, űrlapok készítése és validálása, HTTP kérések kezelése, routing, offline működés, függőség injektálás és tesztelés. Legnagyobb előnye, hogy nagy alkalmazások is készíthetők vele átlátható és karbantartható módon (ha figyelünk a megfelelő konvenciók betartására).

Az Angular alapvetően kliens oldali webalkalmazások fejlesztéséhez készült keretrenszer, de az Angular Universal megjelenése óta lehetőség van Angular alkalmazások szerver oldali renderelésére is, illetve további keretrendszerek használatával natív mobil (pl.: Ionic) és asztali (pl.: Electron) alkalmazásokat is fejleszthetünk Angularral.

## A keretrendszer elemei   

Az alábbi, az Angular honlapjáról származó, ábra jól mutatja, hogy melyek a keretrendszer fontosabb elemtípusai:

![alt text](https://angular.io/generated/images/guide/architecture/overview2.png "Angular architektúra")

Az Angular egyik legnagyobb újdonsága a komponens alapú szemlélet, amely segítségével a webalkalmazást kis egységekből, úgynevezett Componentekből tudjuk felépíteni, amelyek a képernyő egy-egy részeit vezérlik (pl.: menü, táblázat). A kép közepén látható ez az elemtípus, amely két részből áll: TypeScript osztály (az ábrán ez - kicsit szerencsétlenül - Component névvel jelölve) és HTML template (Template feliratú doboz).A két egység között kétirányú kommunikáció (adatkötés) valósítható meg. A Template és a Component között a kapcsolatot az ábrán a Metadata nevű egység adja, ez a gyakorlatban egy TypeScript dekorátor (kb., mint Javaban az annotáció), amelyet a logikát tartalmazó osztályra kell rátennünk, itt tudjuk például megadni a komponensünk nevét, a Metadatanak a függőség injektálás szempontjából van fontos szerepe. Ezeket a komponenseket HTML elemekként tudjuk példányosítani (példa lentebb).

A Directive feliratú doboz egy TypeScript osztályt takar, feladata kettős: egyrészt a DOM-ot lehet rajta keresztül manipulálni, elemeket hozzáadni és elvenni (a laboron az ngIf és ngFor direktívákat fogjuk használni ebből a típusból), másfelől pedig HTML elemek megjelenését tudjuk módosítani a segítségükkel (pl.: egy elem háttérszínét valamilyen logikai feltétel alapján állíthatjuk). A metadata itt is egy TypeScript dekorátor. (A laboron nem fogunk direktívákat készíteni, ezért a bevezetőben erről nem lesz példa)

Általános érvényű szabály a szoftverfejlesztésben, hogy egy osztály egy feladatot lát el, ez az elv igaz az Angularos alkalmazásokra is, ezért igyekszünk minél egyszerűbb és átláthatóbb direktíva és komponens osztályokat írni, az adott osztályhoz szorosan nem kapcsolódó kódot pedig további osztályokba kiszervezni, az ábrán a Service feliratú doboz jelzi ezeket az elemeket, ezek példányosítását is a keretrendszer végzi, típusokat tekintve egyszerű TypeScript osztályok. (A laboron a HTTP kommunikációt fogjuk Service-ekbe szervezni, további példa lentebb.)

Az alkalmazásunk kódját logikai egységekbe, Module feliratú dobozok, tudjuk szervezni, ezek hagyományos TypeScript osztályok dekorátorokkal ellátva. Fontos kiemelni, hogy ez egy Angular által bevezetett fogalom, és nem az ES6-ban megjelent modulról van szó (a kettő funkciója nem azonos, tehát nem váltják ki egymást). A keretrendszer által adott funkciók is ilyen Angularos modulokba rendezve érhetőek el. (példa lentebb)

### Component

Egy egyszerű, „Hello, World” komponens:

```typescript
@Component({
  selector: 'app-root',
  template: `
    <h1>
        Hello, {{title}}!
    </h1>
  `,
  styles: [`h1 {color: red}`]
})
export class AppComponent {
  title = 'World';
}
```

Egy HTML template-ben, mint elem tudjuk használni a dekorátorban megadott selectort használva:

```html
<body>
  <app-root></app-root>
</body>
```

A Component alkotó elemei: TypeScript osztály (AppComponent), amely az üzleti logikát, és a modell objektumokat tartalmazza, HTML template, amely a kinézetet írja le (a példában a title nevű változó értékét jelenítjük meg, a gyakorlatban az osztály publikus változóihoz és függvényeihez van hozzáférésünk a template kódból) és stílusdefiníciók. Mindezek a Component dekorátor segítségével vannak összekapcsolva („@Component”). (A valóságban a logikát, HTML template-et és a stílusdefiníciókat külön fájlokba illik írni, itt csak a példa kedvéért kerültek egy állományba).

A példában nem látszik, de egy Componentnek lehetnek bemenetei és kimenetei. A következő kódrészlet arra mutat példát, hogy hogyan tudjuk a bemeneteket és kimeneteket használni: 

```html
<div class="panel-body" [collapse]="isPanelCollapsed">
  <app-config [configuration]="config" (configurationChange)="configurationChanged($event)"></app-config>
</div>
```

A szögletes zárójelek között levő attribútumok a bemenetek, ezek bármilyen típusú értékek lehetnek (pl.: primitív, összetett, függvény), a kerek zárójelek közöttiek pedig a kimenetek, ezek minden esetben események, amelyeknek egy eseménykezelő függvényt kell értékül adni. A bemenetek értékét kiolvashatjuk és módosíthatjuk. A kimeneti események elsütése a komponensosztályok feladata. A ki- és bemenetek a komponensosztályok attribútumaira/függvényeire képződnek le. A Componentek példányosítását a DI keretrendszer végzi.

### Service

A szolgáltatás mind közül a legáltalánosabb fogalom, lényegében egy TypeScript osztály, amelyet a DI keretrendszer példányosít. Példakód: 

```typescript
@Injectable()
export class LocaleService {

    private readonly defaultLanguage = 'hu';
    private readonly validLanguages = ['hu', 'en'];
    private currentLanguage = this.defaultLanguage;

    constructor(private translate: TranslateService) {}

    public getCurrentLocale() : string {
        return this.defaultLanguage;
    }

    // ...
}
```

A Service osztályokra az Injectable dekorátort kell elhelyeznünk. Az osztály konstruktorában lévő paramétereket a DI keretrendszer fogja feloldani példányosításkor. Az osztály ezektől eltekintve egy hagyományos TypeScript osztály.

### Module

A modulok az Angularos alkalmazások legnagyobb építő elemei. Példa:

```typescript
@NgModule({
    imports: [
      BrowserModule,
      BrowserAnimationsModule,
      TranslateModule.forRoot({
        provide: TranslateLoader,
        useFactory: i18Factory,
        deps: [Http]
      })
    ],
    declarations: [
        AppComponent, LoginComponent, OrdersOverviewComponent,
        OrderComponent, BasicInfoComponent,
    ],
    providers: [
        UserService,
        TokenService
    ]
})
export class AppModule {}
```

Egy Angularos modul egy TypeScript osztály az NgModule dekorátorral ellátva. Maga az osztály lehet teljesen üres, mint a példában is, de lehetséges konfigurációs opciókat is kiajánlania, a példában a TranslateModule a forRoot függvényén keresztül tesz elérhetővé bizonyos beállítási lehetőségeket. Az igazán lényeges részek azonban a dekorátor paramétereiben történnek (imports, declarations és providers), ezek megértéséhez tudnunk kell azt, hogy minden Angularos elem (Component, Directive, Service) pontosan egy modulba tartozik, igaz ez a keretrendszer beépített funkcióira is, például a példában importált BrowserModule a keretrendszerhez tartozik, modulok importálása által tudjuk használni az adott modulhoz beregisztrált elemeket. Az általunk írt elemek regisztrációi a „declarations” (komponensek és direktívák) és „providers” (szolgáltatások) paraméterekhez kerülnek, a regisztráció a függőség injektálás miatt fontos. A modulokban történik tehát az alkalmazásunk függőségeinek „összedrótozása”.
