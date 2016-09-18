
# Yacc֊ի և Lex֊ի մասին

Ես պատմում եմ ծրագրավորման լեզվի շարահյուսական վերլուծիչի իրականացման մասին։ Պատմությունս հնարավորին պարզ պահելու համար ցույց կտամ, թե ինչպես, օրինակ, պարզեցված Բեյսիկ (BASIC) լեզվով գրված ծրագիրը թարգմանել Լիսպ (Lisp) լեզվով գրված ծրագրի։ Բեյսիկն ընտրել եմ իր հայտնիության ու քերականության պարզության համար։ Լիսպը (տվյալ դեպքում՝ դրա Scheme տարատեսակը) նույնպես ընտրել եմ ներկայացման պարզության համա։ Բացի այդ, Լիսպ լեզվով գրված ծրագրերում ուղղակիորեն արտացոլվում է ծրագրի հիերարխիկ (ծառաձև) կառուցվածքը։


## Ովքե՞ր են այդ Yacc֊ն ու Lex֊ը

Yacc֊ը, որ այժմ ավելի հայտնի է GNU Bison իրականացմամբ, շարագյուսական վերլուծիչների գեներատոր է։ Այն հնարավորություն է տալիս դեկլարատիվ լեզվով սահմանել լեզվի շարահյուսական վերլուծիչը, ամեն մի քերականական կանոնի համար սահմանել համապատասխան գործողություններ, նկարագրել հնարավոր շարահյուսական սխալները և այլն։ Yacc֊ը նկարագրությունից գեներացնում է C (կամ մի ուրիշ՝ Go, SML և այլ) լեզվով գրված արդյունավետ ծրագիր։ Գեներացված ծրագիրն արդեն կոմպիլյացվում և կապակցվում (link) է լեզվի իրականացման հիմնական կոդի հետ։ Lex֊ը, որ այժմ հայտնի է GNU Flex իրականացմամբ, նախատեսված է բառային վերլուծիչի գներացման համար։ Սրա համար նույնպես դեկլարատիվվ լեզվով սահմանվում է լեզվի բառային կառուցվածքը, իսկ Lex֊ը գեներացնում է բառային վերլուծիչի արդյունավետ իրականացում C (կամ այլ) լեզվով։

Այս գործիքների մասին մանրամասն կարելի է կարդալ «[Doug Brown, John Levine, Tony Mason, _lex & yacc_, 2nd Edition, O'Reilly Media, 1992](http://shop.oreilly.com/product/9781565920002.do)» և «[John Levine, _flex & bison_, O'Reilly Media, 2009](http://shop.oreilly.com/product/9780596155988.do)» գրքերում։ 


## Ի՞նչ է լեզվի քերականությունը

Քանի որ և՛ վերլուծվող Բեյսիկ լեզուն սահմանելու համար, և՛ շարահյուսական վերլուծիչը GNU Bison ֆայլում կոդավորելու համար օգտագործելու եմ Բեկուսի֊Նաուրի գրառումը (BNF ― Backus-Naur Form), լավ կլինի, որ շատ կարճ խոսեմ նաև դրա մասին։

Ի՞նչ է լեզվի քերականությունը։ Նախ՝ դրա մաթեմատիկական սահմանումը․ _`L` լեզվի `G(L)` քերականությունը `⟨T,N,R,S⟩` քառյակն է, որտեղ `T`֊ն տերմինալային սիմվոլների բազմությունն է, `N`֊ը՝ ոչ տերմինալային սիմվոլներինը, `R` քերականական կանոնների (կամ հավասարումների) բազմությունն է և `S`֊ն էլ սկզբնական սիմվոլն է։_

_Տերմինալային_ սիմվոլները լեզվի քերականության անտրոհելի, ատոմար տարրերն են։ Օրինակ, ծառայողական բառերը, թվային ու տեքստային լիտերալները, մետասիմվոլները և այլն։

_Ոչ տերմինալային_ սիմվոլները լեզվի առանձին տարրերի սահմանումներին տրված անուններն են։

_Քերականական կանոնը_ լեզվի քերականության կառուցման հիմնական միավորն է, դրանով է որոշվում լեզվական կառուցվածքի տեսքը։ Քերականական կանոնը `→` (սլաք) նիշով բաժանված է ձախ ու աջ մասերի։ Ձախ կողմում սահմանվող ոչ տերմինալային սիմվոլն է, իսկ աջում՝ սահմանումը որոշող տերմինալային և ոչ տերմինալային սիմվոլների շարքը։ Օրինակ, Բեյսիկ լեզվի _վերագրման_ հրամանը սահմանող երկու քերականական կանոններն ունեն այսպիսի տեսք․

````
Assignment  → OptionalLet IDENT '=' Expression
OptionalLet → LET | ε
````

Այստեղ գլխատառերով գրված են տերմինալային սիմվոլները՝ `IDENT` և `LET`, իսկ Pascal-Case կանոնով՝ ոչ տերմինալայինները՝ `Assignment`, `Exprsssion` և `OptionalLet`։ Առաջին կանոնն «ասում է», որ վերագրման հրամանը (`Assignment`) բաղկացած է իրար հաջորդող `LET` սիմվոլից, իդենտիֆիկատորից, `=` վերգրման նշանից և վերագրվող արտահայտությունից (`Expression`)։ Երկրորդ կանոնով սահմանվում է `LET` սիմվոլի ոչ պարտադիր լինելը՝ `OptionalLet`֊ը կամ `LET` սիմվոլն է, կամ դատարկ է՝ `ε`։  Քերականական կանոնի աջ կողմում այլընտրանքային տերբերակները (alternatives) իրարից անջատվում են `|` նիշով։

_Սկզբնական սիմվոլն_ այն ոչ տերմինալային սիմվոլն է, որից պետք է սկսել լեզվի վերլուծությունը։ ․․․

Լեզվի քերականության սահմանման այս եղանակը կոչվում է _Բեկուսի֊Նաուրի գրառում_։ 


## Լեզվի սահմանում

Այստեղ քննարկվող Բեյսիկ լեզուն ունի տվյալների միայն մեկ տիպ՝ _իրական թիվ_։ Ծառայողական բառերը գրվում են միայն գլխատառերով, իդենտիֆիկատորներում մեծատառերն ու փոքրատառերը տարբերվում են (case sensitive)։

Բեյսիկի քերականությունը ես կսահմանեմ «վերևից֊ներքև»։ Այսինքն, նախ կսահմանեմ լեզվի «խոշոր» բաղադրիչները, ապա հերթականությամբ կսահմանեմ կանոններում հանդիպող բոլոր չսահմանված ոչ տերմինալային սիմվոլները։

Բեյսիկ լեզվով գրված ծրագիրը ֆունկցիաների հաջորդականություն է․

````
Program → FunctionList
````

Ֆունկցիաների հաջորդականությունը, որ կարող է նաև դատարկ լինել, սահմանեմ ռեկուրսիվ եղանակով․

````
FunctionList → FunctionList Function
             | ε
````

Ֆունկցիա սահմանումով որոշվում է և՛ ֆունկցիայի հայտարարությունը, և՛ ֆունկցիայի սահմանումը․

````
Function → Declaration
         | Definition
````

Ֆունկցիայի հայտարարությունը սկսվում է `DECLARE` ծառայողական բառով, որին հետևում է ֆունկցիայի վերնագիրը․

````
Declaration → DECLARE FunctionHeader
````

Ֆունկցիայի սահմանումը սկսվում է վերնագրով, որին հաջորդում է հրամանների ցուցակ, և ավարտվում է `END` և `FUNCTION` ծառայողական բառերով․

````
Definition → FunctionHeader StatementList END FUNCTION
````

Ֆունկցիայի վերնագիրը սկսվում է `FUNCTION` ծառայողական բառով, որին հետևում է ֆունկցիայի անունը որոշող իդենտիֆիկատոր, ապա՝ `(` և `)` փակագծերի մեջ վերցրած պարամետրերի ցուցակ։

````
FunctionHeader → FUNCTION IDENT '(' ParameterList ')' NewLines
````

Պարամետրերի ցուցակը կամ դատարկ է, կամ էլ ստորակետով իրարից բաժանված իդենտիֆիկատորների հաջորդականություն է․

````
ParameterList → IdentifierList
              | ε
````

Իդենտիֆիկատորների ցուցակը նույնպես սահմանեմ ռեկուրսիվ եղանակով․

````
IdentifierList → IdentifierList ',' IDENT
               | IDENT
````

`NewLines` ոչ տերմինալային սիմվոլով որոշվում է նոր տողի անցման մեկ և ավելի նիշերի շարքը․

՝՝՝՝
NewLines → NewLines '\n'
         | '\n'
՝՝՝՝

Հրամանների ցուցակնը, որն էլի սահմանում եմ ռեկուրսիվ կանոնով, կարող է դատարկ լինել․

````
StatementList → StatementList Statement NewLines
              | ε
````

Բեյսիկի հրամաններն են․ ներմուծում, արտածում, վերագրում, ճյուղավորում, պարամետրով ցիկլ, նախապայմանով ցիկլ, ենթածրագրի կանչ։ Դրանք բոլորը սահմանում եմ որպես `Statement`։

Ներմուծման հրամանը սկսվում է `INPUT` ծառայողական բառով, որին հաջորդվում է ներմուծվող փոփոխականի իդենտիֆիկատորը

````
Statement → INPUT IDENT
````

Արտածման հրամանը սկսվում է `PRINT` բառով, որին հետևում է արտածվող արտահայտությունը․

````
Statement → PRINT Expression
````

Վերագրման հրամանն արդեն սահմանել եմ վերևում, այստեղ պարզապես կրկնեմ այն․

````
Statement → OptionalLet IDENT '=' Expression
OptionalLet → LET | ε
````

Ճյուղավորման հրամանը բոլորիս լավ հայտնի `IF` կառուցվածքն է։ Այն բաղկացած է երեք կարևոր բլոկներից, որոնցից միայն առաջինն է պարտադիր։ Առաջին և պարտադիր բլոկը սկսվում է `IF` ծառայողական բառով, որին հետևում է ճյուղավորման պայմանի արտահայտությունը, հետո՝ `THEN` ծառայողական բառը, նոր տողերի նիշեր և պայմանի ճշմարիտ լինելու դեպքում կատարվող հրամանների ցուցակ։ Երկրորդ և ոչ պարդադիր բլոկը այլընտրանքային պայմանները որոշող `ElseIfPartList` ցուցակն է, որի ամեն մի էլեմենտը սկսվում է `ELSEIF` ծառայողական բառով, ապա՝ պայմանի արտահայտությունը, `THEN` ծառայողական բառը, նոր տողի նիշեր և պայմանի ճշմարիտ լինելու դեպքում կատարվող հրամանների ցուցակ։ Երրորդ և ոչ պարտադիր բլոկը սկսվում է `ELSE` ծառայողական բառով, որին հաջորդում են նոր տողի նիշեր և հրամանների շարք։ Ճյուղավորման ամբողջ կառուցվածքն ավարտվում է `END` և `IF` ծառայողական բառերով։ 

````
Statement → IF Expression THEN NewLines StatementList ElseIfPartList ElsePart END IF
ElseIfPartList → ElseIfPartList ELSEIF Expression THEN NewLines StatementList
               | ε
ElsePart → ELSE StatementList
		 | ε
````

Պարամետրով ցիկլի հրամանը սկսվում է `FOR` ծառայողական բառով, որին հաջորդում են ցիկլի պարամետրի իդենտիֆիկատորը, `=` նիշը, պարամետրի սկզբնական արժեքը որոշող արտահայտությունը, `TO` բառը, պարամետրի արժեքի վերին սահմանի արտահայտությունը, `STEP` բառը, պարամետրի քայլը որոշող արտահայտությունը, նոր տողի նիշեր, ցիկլի մարմինը որոշող հրամանների ցուցակ։ Պարամետրով ցիկլի հրամանն ավարտվում է `END` և `FOR` բառերով։

````
Statement → FOR IDENT '=' Expression TO Expression OptionalStep NewLines StatementList END FOR
OptionalStep → STEP Expression
````

Նախապայմանով ցիկլի հրամանը սկսվում է `WHILE` ծառայողական բառով, որին հետևում են ցիկլի կրկնման պայմանի արտահայտությունը, նոր տողի նիշեր, ցիկլի մարմնի հրամանների շարք։ Հրամանն ավարտվում է `END` և `WHILE` բառերով։

````
Statement → WHILE Expression NewLines StatementList END WHILE
````

Ենթածրագրի կանչը սկսվում է `CALL` բառով, որին հետևում են ֆունկցիայի անունի իդենտիֆիկատորը և արգումենտների ցուցակը (այն կարող է և դատարկ լինել)․

````
Statement → CALL IDENT ArgumentList
ArgumentList → ExpressionList
             | ε
````

Արտահայտությունների ցուցակը ստորակետով իրարից անջատված արտահայտություննների շարք է․

````
ExpressionList → ExpressionList ',' Expression
               | Expression
````

Հրամաններն այսքանն էին։ Անցնեմ _արտահայտությունների_ սահմամնմանը։ Դրանք կարող են պարունակել թվաբանական, տրամաբանական ու համեմատման գործողություններ, ինչպես նաև ֆունկցիայի կանչ։ Բացի բացասման ու ժխտման գործողություններից, մյուս գործողությունները բինար են։

Որպեսզի արտահայտության քերականության մեջ արտացոլվի գործողությունների բնական (ընդունված) բաշխականությունն (associativity) ու նախապատվությունը (precedence), քերականությունը բաժանված է մի քանի մակարդակների։

՝՝՝՝
Expression     → Conjunction OR Conjunction
Conjunction    → Equality AND Equality
Equality       → Comparison ('='|'<>') Comparison
Comparison     → Addition ('>'| '>=' | '<' | '<=') Addition
Addition       → Multiplication ('+'|'-') Multiplication
Multiplication → Power ('*' | '/') Power
Power          → Factor ['^' Power]
Factor         → IDENT '(' ArgumentList ')'
               | '(' Expression ')'
               | '-' Factor
               | NOT Factor
			   | NUMBER
			   | IDENT
՝՝՝՝

Ես այստեղ շեղվեցի BNF֊ի սովորական գրառումից, պարզապես ցույց տալու համար արտահայտություններում հանդիպող գործողությունների բաշխականությունն ու նախապատվությունը։ Սակայն Bison֊ը հնարավորություն է տալիս նույն հասկացությունների սահմանել ավելի հարմր մեխանիզմներով։ Այդ մասին՝ իր տեղում։

Այսքանը լեզվի սահմանման մասին։ Կարծում եմ, որ ավելի մանրամասն նկարագրությունը պարզապես ավելորդ կլիներ։


## GNU Bison֊ի ֆայլը

Yacc֊ի և GNU Bison֊ի մուտքին տրվող ֆայլը սովորաբար ունի ունի `.y` վերջավորությունը (երբեմն օգտագործում են նաև `.yacc`, բայց ինձ `.y` ձևն ավելի է դուր գալիս)։ Այդ ֆայլը բաղկացած է երեք հտվածներից, որոնք իրարից բաժանվում են `%%` նիշերը պարունակող տողերով։

````
սահմանումներ
%%
քերականական կանոններ
%%
օժանդակ ֆունկցիաներ
````

Առաջին հատվածում սահմանումներն են, մասնավորապես, այստեղ են սահմանվում տերմինալային սիմվոլները, և սկզբնական սիմվոլը։ Երկրորդ բաժնում թվարկվում են քերականական կանոնները։ Իսկ երրորդն էլ C լեզվով գրված օժանդակ ֆունկցիաների համար է։ Առայժմ ես ցուցադրեմ միայն առաջին ու երկրորդ բաժինները։

Որպես օրինակ ցույց տամ միայն արտահայտությունների վերլուծիչի համար գրված `.y` ֆայլը։ Այն պարունակում վերը նկարագրված Բեյսիկ լեզվի արտահայտությունների քերականությունը և դրանում օգտագործված տերմինալային սիմվոլների թվարկումը։

Ստեղծում եմ `expr.y` ֆայլը և `%%` նիշերով այն բաժանում եմ երկու մասի։ Առաջին մասում `%token`, `%left`, `%right` և `%nonassoc` հրահանգներով թվարկում եմ տերմինալային սիմվոլները։ `%token`֊ը պարզապես հայտարարում է տերմինալային սիմվոլ։ (Bison֊ի ֆայլի ընթեռնելիությունը պարզեցնելու համար ես տերմինալային սիմվոլները գրելու եմ ոչ թե գլխատառերով, այլ `x` տառով սկսվող camel-case֊ով։)

````
%token xNumber
%token xIdent
````

`%left`֊ը և `%right`֊ը ցուց են տալիս, որ իրենց սահմանած տերմինալային սիմվոլը (տվյալ դեպքում, գործողության անունը) ունի համապատասխանաբար ձախ կամ աջ բաշխականություն։ `%nonassoc` հրահանգի սահմանած տերմինալային սիմվոլները բաշխականություն չունեն։ Գործողությունների նախապատվությունը սահմանվում է ըստ դրանց թվարկման կարգի՝ ցածրից դեպի բարձր։

Հետևյալ սահմանումներում ձախ բաշխականություն ունեն կոնյունկցիան, դիզյունկցիան, գումարման, հանման, բազմապատկման ու բաժանման գործողությունները։ Համեմատման վեց ործողությունները բաշխականություն չունեն։ Աստիճան բարձրացնելու բինար գործողությունը և ժխտման ունար գործողությունն ունեն աջ բաշխականություն։

````
%left xOr
%left xAnd
%nonassoc xEq xNe
%nonassoc xGt xGe xLt xLe
%left xAdd xSub
%left xMul xDiv
%right xPow
%right xNot
````

Թվարկված գործողություններից ամենացածր նախապատվությունն ունի դիզյունկցիայի `OR` գործողությունը, իսկ ամենաբարձրը՝ `NOT` գործողությունը։ Նույն տողի վրա գրված են հավասար նախապատվություն ունեցող գործողությունները։

Հիմա գրում եմ `.y` ֆայլի երկրորդ բաժինը։ Այստեղ պարզապես պետք է քերականական կանոններով սահմանել, թե ինչպես են արտահայտությունները գործողծողություններով կապված իրար։ Bison֊ի ֆայլում քերականական կանոնների ձախ ու աջ մասերն իրարից բաժանվում են `:` նիշով, և ամեն մի կանոն ավարտվում է `;` նիշով։

````
Expression
    : Expression xOr Expression
	| Expression xAnd Expression
	| Expression xEq Expression
	| Expression xNe Expression
	| Expression xGt Expression
	| Expression xGe Expression
	| Expression xLt Expression
	| Expression xLe Expression
	| Expression xAdd Expression
	| Expression xSub Expression
	| Expression xMul Expression
	| Expression xDiv Expression
	| Expression xPow Expression
	| '(' Expression ')'
	| xIdent '(' ArgumentList ')'
	| '-' Expression %prec xNot
	| xNot Expression
	| xNumber
	| xIdent
	;
````

Ոչ մի արտասովոր բան․ պարզապես բոլոր նախատեսված գործողությունների համար նշված է, թե նրանց օպերանդ֊արտահայտությունները ինչ շարահյուսական դիրքում են գտնվում գործողության նշանի նկատմամբ։ Միայն հետևյալ կանոնն է մի քիչ անսովոր․

````
Expression : ...
           | '-' Expression %prec xNot
````

բայց դրա բացատրությունն էլ է պարզ։ Այստեղ `%prec` հրահանգով նշված է, որ բացասման (ունար մինուս) գործողությունը պետք է ունենա նույն բաշխականությունը, ինչ որ ժխտման `NOT` գործողությունը։

Հիմա ՝expr.y` ֆայլը տամ Bison֊ի մուտքին․

````
$ bison expr.y
expr.y:31.22-33: error: symbol ArgumentList is used, but is not defined as a token and has no rules
         | xIdent '(' ArgumentList ')'
                      ^^^^^^^^^^^^
````

Սխալի մասին հաղորդագրությունն ասում է, որ `ArgumentList` սիմվոլն օգտագործված է առանց սահմանման։ Լրացնեմ այդ սահմանումը ևս․ ֆունկցիայի կանչի արգումենտների ցուցակը կամ դատարկ է, կամ ստորակետով իրարից անջատված արտահայտությունների ցուցակ է․

````
ArgumentList
    : ExpressionList
	| /* empty */
	;

ExpressionList
    : ExpressionList ',' Expression
	| Expression
	;
````

Նշեմ, որ `.y` ֆայլերում դատարկ կանոն սահմանելու հատուկ սիմվոլ, ինչպիսին BNF֊ում ε տառն է, չկա։ Պարզապես պետք է կանոնի աջ մասը դատարկ թողնել։ Ես օգտագործում եմ `/* empty */` (երբեմն էլ՝ `/* epsilon */`) մեկնաբանությունը, որպեսզի ավելի հեշտ գտնեմ դատարկ կանոնները։

Այս վերջն լրացումից հետո `expr.y` ֆայլը նորից Bison֊ի մուտքին տալով տեսնում եմ, որ գոնե քերականության տեսակետից ամեն ինչ կարգին է․ Bison-ը այլևս բողոքներ չունի։


## Քերականության ստուգումը Bison֊ի միջոցով

Վերադառնամ իմ հիմնական գործին։ Երբ Բեյսիկ լեզվի սահմանման հետ արդեն ամեն ինչ պարզ է, ես պիտի փորձեմ դրա քերականությունը ստուգել Bison֊ի միջոցով (ինչպես դա արեցի արտահայտությունների համար)։

Ստեղծում եմ `parser.y` ֆայլը (սա արդեն լինելու է Բեյսիկի շարահյուսական վերլուծիչի հիմնական նկարագրությունը) և գրա մեջ Bison֊ի BNF կանոններով գրառում եմ Բեյսիկի ամբողջ քերականությունը։ Ահա այն․

````
/* parser.y */

%token xIdent
%token xNumber

%token xDeclare
%token xFunction
%token xLet
%token xInput
%token xPrint
%token xIf
%token xThen
%token xElseIf
%token xElse
%token xEnd
%token xFor
%token xTo
%token xStep
%token xWhile
%token xCall

%left xOr
%left xAnd
%nonassoc xEq xNe
%nonassoc xGt xGe xLt xLe
%left xAdd xSub
%left xMul xDiv
%right xPow
%right xNot

%token xEol
%token xEof

%start Program
%%
Program
    : FunctionList xEof
	;

FunctionList
    : FunctionList Function
	| /* empty */
	;

Function
    : xDeclare FunctionHeader
    | FunctionHeader StatementList xEnd xFunction NewLines
	;

FunctionHeader
    : xFunction xIdent '(' ParameterList ')' NewLines
	;

ParameterList
    : IdentifierList
	| /* empty */
	;

NewLines
    : NewLines xEol
	| xEol
	;

IdentifierList
    : IdentifierList ',' xIdent
	| xIdent
	;

StatementList
    : StatementList Statement NewLines
	| /* empty */
	;

Statement
    : xInput xIdent
	| xPrint Expression
	| OptionalLet xIdent xEq Expression
	| xIf Expression xThen NewLines StatementList ElseIfPartList ElsePart xEnd xIf
	| xFor xIdent xEq Expression xTo Expression OptionalStep NewLines StatementList xEnd xFor
	| xWhile Expression NewLines StatementList xEnd xWhile
	| xCall xIdent ArgumentList
	;

OptionalLet
    : xLet
	| /* empty */
	;

ElseIfPartList
    : ElseIfPartList xElseIf Expression xThen NewLines StatementList
	| /* empty */
	;

ElsePart
    : xElse NewLines StatementList
	| /* empty */
	;

OptionalStep
    : xStep Expression
	| /* empty */
	;

ArgumentList
    : ExpressionList
	| /* empty */
	;

ExpressionList
    : ExpressionList ',' Expression
	| Expression
	;

Expression
    : Expression xOr Expression
	| Expression xAnd Expression
	| Expression xEq Expression
	| Expression xNe Expression
	| Expression xGt Expression
	| Expression xGe Expression
	| Expression xLt Expression
	| Expression xLe Expression
	| Expression xAdd Expression
	| Expression xSub Expression
	| Expression xMul Expression
	| Expression xDiv Expression
	| Expression xPow Expression
	| '(' Expression ')'
	| xIdent '(' ArgumentList ')'
	| '-' Expression %prec xNot
	| xNot Expression
	| xNumber
	| xIdent
	;
````

Նորություն է միայն ֆայլի առաջին բաժնի վերջում գրված `%start Program` հրահանգը։ Սրանով նշվում է, որ սահմանված քերականության սկզբնական սիմվոլը `Program` ոչ տերմինալային սիմվոլն է։ 

`parser.y` ֆայլը Bison֊ի մուտքին տալու հենց առաջին փորձից պարզվում է, որ ամեն ինչ կարգին է, Bison֊ը քերականության տեսակետից բողոքներ չունի։ 

Ի՞նչ ունեմ այս պահին։ Bison֊ի լեզվով գրված Բեյսիկի քերականությունը, որ հակասություններ կամ սխալներ չի պարունակում, և պատրաստ է վերածվելու լիարժեք շարահյուսական վերլուծիչի։

Ո՞րն է իմ հաջորդ քայլը և ի՞նչ է պակասում դրան անցնելու համար։ Հիմա ես պետք է գրեմ բառային վերլուծիչ (կամ՝ լեքսիկական անալիզատոր, lexical analyzer, scanner), որը Bison֊ին է «մատակարարելու» սահմանված տերմինալային սիմվոլները։ Ապա բառային ու շարահյուսական վերլուծիչների համադրմամբ ստանամ մի նախնական ծրագիր, որը «հաստատում» է, որ Բեյսիկ լեզվով գրված ծրագրերը կարող են վերլուծվել, իսկ եթե չեն կարող՝ տեղեկացնի սխալների մասին։ Այլ կերպ ասած՝ իմ առաջիկա նպատակը Բեյսիկով գրված ծրագրի՝ Բեյսիկի քերականությանը համապատասխանող լինելը ստուգող գործիքն է։ ․․․


## Բառային վերլուծություն Flex֊ի միջոցով

Flex֊ի համար գրված նկարագրության ֆայլերն ունենում են `.l` վերջավորությունը (երբեմն նաև՝ `*.lex`)։ Bison֊ի ֆայլի պես Flex֊ի ֆայլն էլ է `%%` նիշերով բաժանվում երեք հատվածների։ Առաջինում սահմանումներն են, երկրորդում՝ թոքենները (տերմինալային սիմվոլներ) ճանաչելու կանոնները, երրորդում՝ օժանդակ ֆունկցիաները։

````
սահմանումներ
%%
կանոններ
%%
ֆունկցիաներ
````

Սահմանումների հատվածն օգտագործվում է բարդ կանոնավոր արտահայտություններին կարճ անուններ տալու համար։ Այսպես․

````
number      [0-9]+(\.[0-9]+)?
ident       [a-zA-Z][a-zA-Z0-9]*
comment     \'.*$
````

Այստեղ `numebr`֊ը սահմանված է որպես տասնորդական կետ պարունակող իրական թիվ, `ident`֊ը՝ որպես տառով սկսվող և կառերից ու թվերից բաղկացած հաջորդականություն, իսկ `comment`֊ը `'` նիշով սկսվող և մինչև տողի վերջը շարունակվող նիշերի հաջորդականություն։

Երկրորդ բաժնում գրվում են թոքենները ճանաչող կանոնները։ Flex֊ի կանոնը բաղկացած է երկու մասից․ թոքենի նկարագրիչ (pattern) և գործողություն (action)։

````
pattern     action
````

Նկարագրիչը կամ կանոնավոր արտահայտություն է, կամ սահմանումների բաժնում սահմանված անուն։ Երկրորդ դեպքում անունը պետք է վերցնել `{` և `}` փակագծերի մեջ։ Այսպես․

````
[ \t]       { /**/ }
{comment}   { /**/ }
{number}    { return xNumber; }
````

Առաջին ու երկրորդ կանոններն «ասում» են, որ պետք է անտեսել բացատանիշերն ու մեկնաբանությունները։ Երրորդ կանոնն ասում է, որ պետք է վերադարձնել `xNumber` թոքենը, եթե հանդիպել է `number` սահմանումով նկարագրիչը (`number`֊ը և `comment`֊ը սահմանվել են ֆայլի առաջին բաժնում)։

Ծառայողական բառերի համար վերադարձվում են իրենց համապատասխան թոքենները․

````
"DECLARE"   { return xDeclare; }
"FUNCTION"  { return xFunction; }
"LET"       { return xLet; }
"INPUT"     { return xInput; }
"PRINT"     { return xPrint; }
"IF"        { return xIf; }
"THEN"      { return xThen; }
"ELSEIF"    { return xElseIf; }
"ELSE"      { return xElse; }
"END"       { return xEnd; }
"FOR"       { return xFor; }
"TO"        { return xTo; }
"STEP"      { return xStep; }
"WHILE"     { return xWhile; }
"CALL"      { return xCall; }
"OR"        { return xOr; }
"AND"       { return xAnd; }
"NOT"       { return xNot; }
````

Քանի որ Flex֊ը թոքենների նկարագրիչները ստուգում է վերևից ներքև, իդենտիֆիկատորները ճանաչող կանոնը պետք է գրել ծառայողական բառերից հետո․

````
{ident}     { return xIdent; }
````

Հաջորդ խմբում գործողությունների նշանակումները ճանաչող կանոններն են, որոնք նույպես վերադարձնում են ամեն մի գործողությանը համապատասխանեցված թոքենը։

````
"="         { return xEq; }
"<>"        { return xNe; }
">"         { return xGt; }
">="        { return xGe; }
"<"         { return xLt; }
"<="        { return xLe; }
"+"         { return xAdd; }
"-"         { return xSub; }
"*"         { return xMul; }
"/"         { return xDiv; }
"^"         { return xPow; }
\n          { return xEol; }
````

Flex֊ի կանոնավոր արտահայտություններում `.` (կետ) նիշը համապատասխանում է կամայական նիշի, բացի նոր տողի նիշից։ Գրելով հետևյալ կանոնը․

````
.           { return (int)yytext[0]; }
````

որպես թոքեն վերադարձնում եմ տվյալ նիշի համապատասխան ASCII կոդը։ Բանն այն է, որ Flex֊ը հերթական նկարագրիչին համապատասխանող տեքստը պահում է `yytext` բուֆերի մեջ։ Քիչ հետո այս բուֆերի օգտագործման այլ օրինակներ էլ ցույց կտամ։

Եվ վերջապես, ֆայլի ավարտին համապատասխանում է `<<EOF>>` հատուկ նշանակումը։ Դրա համար էլ վերադարձվում է `xEof` թոքենը։

````
<<EOF>>      { return xEof; }
````

Հիմա արդեն ժամանակն է Flex֊ի միջացով ստուգել նկարագրված բառային վերլուծիչի ճշտությունը։ Դրա համար պետք է `scanner.l` ֆայլը տալ Flex֊ի մուտքին․

````
$ flex scanner.l
````

Եթե Flex֊ը սխալների մասին հաղորդագրություններ չի արտածում, ապա ամեն ինչ կարգին է․ կարելի է գնալ առաջ։


## Կապակցման առաջին փորձ

Bison֊ն իրեն տրված քերականության նկարագրությունից գեներացնում է C կոդ։ Եթե `.y` ֆայլն ունի `⟨name⟩` անունը, ապա Boson֊ը լռելությամբ գեներացնում է `⟨name⟩.tab.c` ֆայլը։ Գեներացրած շարահյուսական վերլուծիչի մուտքի կետը `yyparse()` ֆունկցիան է։ Flex-ն էլ իրեն տրված նկարագրությունից գեներացնում է C կոդ։ Նրա գեներացրած ֆայլը լռելությամբ ունի `lex.yy.c` անունը, բայց ես սովորություն ունեմ Flex֊ի `-o` պարամետրով `⟨name⟩` անունի համար գեներացնել `⟨name⟩.yy.c` ֆայլը։ Flex֊ի գեներացրած բառային վերլուծիչի մուտքի կետը `yylex()` ֆունկցիան է։ `yyparse()` ֆունկցիան իրեն անգհրաժեշտ հերթական թոքենը ստանալու համար պարբերաբար կանչում է `yylex()` ֆունկցիան։

Bison֊ի և Flex֊ի գեներացրած ֆայլերն իրար հետ կոմպիլյացնելու ու կատարվող մոդուլ ստանալու համար պետք է պիտի գրեմ նաև `main()` ֆունկցիա, որում կանչվում է `yyparse()` ֆունկցիան։ Ահա այն․

````c
/* main.c */
int main()
{
  extern int yyparse();
  int ok = yyparse();
  return ok;
}
````

Երբ GNU GCC կոմպիլյատորորով փորձում եմ թարգմանել (compile) ու կապակցել (link) `parser.tab.c`, `scanner.yy.c` և `main.c` ֆայլերը, ստանում եմ սխալների հաղորդագրությունների մի շարք։ Ահա դրանցից առաջին չորսը․

````
scanner.l: In function ‘yylex’:
scanner.l:9:10: error: ‘xNumber’ undeclared (first use in this function)
 {number}    { return xNumber; }
          ^
scanner.l:9:10: note: each undeclared identifier is reported only once for each function it appears in
scanner.l:10:10: error: ‘xDeclare’ undeclared (first use in this function)
 "DECLARE"   { return xDeclare; }
          ^
scanner.l:11:10: error: ‘xFunction’ undeclared (first use in this function)
 "FUNCTION"  { return xFunction; }
          ^
scanner.l:12:10: error: ‘xLet’ undeclared (first use in this function)
 "LET"       { return xLet; }
          ^
....
````

Տեսնում եմ, որ կոմպիլյատորը չի գտնում `yylex()` ֆունկցիայում օգտագործված թոքենները (դրանք սահմանված էին `parser.y` ֆայլում)։ Բանն այն է, որ Flex֊ի և Bison֊ի գեներացրած C ֆայլերը կոմպիլյացիայի տարբեր մոդուլներ են, և բնական է, որ կոմպիլյատորը չի կարող դրանցից մեկը թարգմանելիս «տեսնել» մյուսում սահմանված անունները։ Bison֊ի հրամանային տողի `-d` պարամետրը `⟨name⟩.y` ֆայլի համար գեներացնում է նաև `⟨name⟩.tab.հ` ֆայլը, որը պարունակում է `⟨name⟩.y`֊ում հայտարարված թոքենների (նաև այլ օբյեկտների) հայտարարությունները։ Ուրեմն `parser.y` ֆայլը պետք է Bison֊ով թարգմանել հետևյլ հրամանով․

````bash
$ bison -d parser.y
````

որի արդյունքում կգեներացվեն `parser.tab.h` և `parser.tab.c` ֆայլերը։ Հետո պետք է `parser.tab.h` ֆայլը կցել `scanner.l` ֆայլին։

Ե՛վ Bison֊ի, և՛ Flex֊ի համար նախատեսված ֆայլերի սկզբում թույլատրվում է ունենալ C լեզվով գրված կոդի բլոկ։ Այդ բլոկը սկսվում է `%{` նիշերով և վերջանում է `%}` նիշերով և առանց փոփոխության պատճենվում է գեներացված `.c` ֆայլի մեջ։ Այսինքն, `.l` և `.y` ֆայլերը ունեն հետևյալ տեսքը․

````
%{
C կոդ
%}
սահմանումներ
%%
քերականական/լեքսիկական կանոններ
%%
օժանդակ C ֆունկցիաներ
````

Հենց այս `%{...%}` բլոկում էլ պետք է `#include` հրահանգով `scanner.l` ֆայլին կցել `parser.tab.h` ֆայլը։ Այսինքն, `scanner.l` ֆայլի սկիզբը պետք է ունենա հետևյալ տեսքը․

````
%{
#include "parser.tab.h"
%}

%option noyywrap

number      [0-9]+(\.[0-9]+)?
ident       [a-zA-Z][a-zA-Z0-9]*
comment     \'.*$
````

Քանի որ խոսք է բացվել `scanner.l` ֆայլը լրացնելու մասին, բացատրեմ նաև `%option noyywrap` տողը։ Երբ Flex֊ի գեներացրած բառային վերլուծիչը կարդում է վերլուծվող ֆայլը և հասնում է դրա վերջին, այն կանչում է `yywrap()` ֆունկցիան։ Եթե վերջինս վերադարձնում է `0`, ապա վերլուծությունը շարունակվում է, իսկ եթե վերադարձնում է `1`, ապա `yylex()` վերադարձնում է `0` արժեք և վերլուծությունը դադարեցվում է։ `%option noyywrap` հրահանգով Flex֊ին խնդրում ենք երբեք չկանչել `yywrap()` ֆունկցիան։ Իսկ ֆայլի վերջը «բռնելու» համար օգտագործում եմ `<<EOF>>` նկարագրիչը։

`scanner.l` ֆայլում ուղղումներ անելուց հետո նորից փորձեմ կառուցել կատարվող մոդուլը․

````bash
$ bison -d parser.y
$ flex -oscanner.yy.c scanner.l
$ gcc *.c
````

Կոմպիլյատորը նորից տեղեկացնում է սխալների մասին։

````
parser.tab.c: In function ‘yyparse’:
parser.tab.c:1259:16: warning: implicit declaration of function ‘yylex’ [-Wimplicit-function-declaration]
       yychar = yylex ();
                ^
parser.tab.c:1388:7: warning: implicit declaration of function ‘yyerror’ [-Wimplicit-function-declaration]
       yyerror (YY_("syntax error"));
       ^
````

Առանց սահմանվելու (կամ հայտարարվելու) օգտագործվել են `yylex()` և `yyerror()` ֆունկցիաները։ `yylex()`֊ի դեպքում ամեն ինչ պարզ է․ այն գտնվում է ուրիշ կոմպիլյացիայի միավորում (compilation unit)։ Պարզապես պետք է `parser.y` ֆայլի սկզբում հայտարարել `yylex()`ֆունկցիան։ `yyerror()` ֆունկցիան օգտագործվում է սխալների մասին ազդարարելու համար․ այն ևս պետք է հայտարարել `parser.y` ֆայլի սկզբում։

````
%{
extern int yylex();
static int yyerror( const char* );
%}

%token xIdent
%token xNumber
....
````

Դե, `yylex()` ֆունկցիան գեներացվում է Flex֊ի օգնությամբ, իսկ `yyerror()`֊ը պետք է սհամանի ծրագրավորողը։ `parser.y` ֆայլի օժանդակ ֆունկցիաների բաժինը ժիշտ այն տեղն է, որտեղ պետք է սահմանել շարահյուսական վերլուծիչում օգտագործվող `yyerror()` ֆունկցիան։

````
%%

static int yyerror( const char* message )
{
  fprintf(stderr, "ՍԽԱԼ։ %s\n", message);
  return 1;
}
````

Հա, չմոռանամ նաև ֆայլի սկզբում կցել `stdio.h` ֆայլը՝ C լեզվի ստանդարտ գրադարանի ներմուծման֊արտածման գործողությունների համար։

````
%{
#include <stdio.h>

extern int yylex();
static int yyerror( const char* );
%}

%token xIdent
%token xNumber
....
````



----

Makefile


````make
SOURCES=main.c scanner.yy.c parser.tab.c

all: $(SOURCES)
	gcc -gdwarf-2 -obasic-s $(SOURCES)

scanner.yy.c: scanner.l
	flex -oscanner.yy.c scanner.l

parser.tab.c parser.tab.h: parser.y
	bison -d parser.y
````



