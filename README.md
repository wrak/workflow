# workflow

Práce na feature
---
Před začátkem práce si pullnu čerstvý master a novou větev postavím nad ním.

`
git checkout master
git pull origin master
`
Nové větve by měli obsahovat mé iniciály a číslo tasku

`git checkout -b pr-ZO-4444-add-commenting`

Snažím se commitovat co nejčastěji. Na konci dne/práce použiju squashing přes rebase interactive, tím získám co nejatomičtější commity. *Atomický commit převede aplikaci z jednoho funkčního stavu do druhého.* Při CR není pak nutné dělat review celého balíku změn najednou a při review jednotlivých commitů si můžz být jistý, že kód, který jsem zkontroloval, se v dalších commitech nezmění. 

`git rebase -i origin/master`

Dostanu např.

`
pick 0084050 add comment model
pick b00ae30 update post controller
pick 527c74d oops forgot update phpDoc
pick 6d78bbf tests
pick 8601b30 fix: some bug in comment model
pick a1810a9 view for comments
`

změním na

`
pick 0084050 add comment model
f 8601b30 fix: some bug in comment model # squash but leave previous commit message
pick 6d78bbf tests
pick b00ae30 update post controller
f 527c74d oops forgot update phpDoc # squash but leave previous commit message
pick a1810a9 view for comments
`

Po dokončení a před pushem na remote ještě udělám rebase na master (pro případm že se v průběhu mého vývoje změnil)

`
git pull origin master
git rebase origin/master
`

Spustím testy a zelenou větev můžu pushnout

`git push`

Epic feature
---
Pro epic feature založíme speciální větev, kterou pravidelně rebasujeme na master. Práce na jednotlivých feature bude stejná jako nad masterem (viz.: *Práce na feature*) s tím rozdílem, že budu pracovat nad epic větví. Po dokončení feature (a rebase nad epic) rovnou mergujeme do epic větve

BC Breaks
---
Změna, která by má za následek BC break pro ostatní větve, by měla jít okamžitě do masteru, aby ostatní programátoři mohli rebasnout na novou verzi.
Typicky se jedná o změnu schématu/API, kdy starší verze aplikace nebudou reflektovat nový stav schématu/API.

Testování
---
Pokusím se o rebase nad aktuální master

`
git pull origin master
git rebase origin/master
`

Pokud vše proběhlo bez konfliktů, spustím testy. V opačném případě poprosím o rebase autora větve a poté spustím testy.

V případě, že najdu chyby, vrátím issue zpět a popíšu problémy. 

Integrace
---
Založím si release větev ve tvaru release/1.5.4

`git checkout -b release/1.5.4`

Checkoutnu si na první větev připravenou k nasazení, vytvořím si její kopii ve tvaru `release/<vetev>`, kterou pushnu, a tuto kopii se pokusím rebase-nout na release větev a spustím testy. Pokud při rebase narazím na konflikt, nechám rebase a spuštění testů udělat autora větve. V tomto případě je nutné, aby autor větve toto udělal okamžitě. Pokud automatické testy neprojdou, větev se nebude nasazavoat, issue se přesune zpět do working a popíše se problém. Pokud nová feature neprojde přes CR, větev se nebude nasazovat, issue se přesune zpět do working a popíše se problém. Pokud je vše v pořádku, udělám merge do release větve. Pokud má větev více než jeden commit, použiju `--no-ff` modifikátor

Výše popsaný proces aplikuji na všechny feature, které jsou připravené k nasazení. 

Větev se nasadí na preprod, kde se ještě jednou spustí testy a provede se testování testerem. Pokud by z nějakého důvodu release větev obsahuje po testování chyby, vyhledá se zdroj problému a celá release větev se zahazuje. Tato situace je třeba řešit operativně s programátory, jejichž větvě jsou součástí release.

Pokud je vše v pořádku, release větev se mergne do masteru a provede se release. Release větev a feature větve se smažou

Bugfixes a operativa
---
Bugfixy a operativa se staví rovnou nad masterem a nasazují se okamžitě mimo release cyklus.


