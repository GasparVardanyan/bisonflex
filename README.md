
# Yacc֊ի և Lex֊ի մասին

Ես պատմում եմ ծրագրավորման լեզվի շարահյուսական վերլուծիչի իրականացման մասին։ պատմությունս հնարավորին պարզ պահելու համար ցույց կտամ, թե ինչպես, օրինակ, պարզեցված Բեյսիկ (BASIC) լեզվով գրված ծրագիրը թարգմանել Լիսպ (Lisp) լեզվով գրված ծրագրի։ Բեյսիկն ընտրել եմ իր հայտնիության ու քերականության պարզության համար։ Լիսպը (տվյալ դեպքում՝ դրա Scheme տարատեսակը) նույնպես ընտրել եմ ներկայացման պարզության համա։ Բացի այդ, Լիսպ լեզվով գրված ծրագրերում ուղղակիորեն արտացոլվում է ծրագրի հիերարխիկ (ծառաձև) կառուցվածքը։


## Ի՞նչ է լեզվի քերականությունը

Քանի որ և՛ վերլուծվող Բեյսիկ լեզուն սահմանելու համար, և՛ շարահյուսական վերլուծիչը GNU Bison ֆայլում կոդավորելու համար օգտագործելու եմ Բեկուսի֊Նաուրի գրառումը (BNF ― Backus-Naur Form), լավ կլինի, որ շատ կարճ խոսեմ նաև դրա մասին։

Ի՞նչ է լեզվի քերականությունը։ Նախ՝ դրա մաթեմատիկական սահմանումը․ _`L` լեզվի `G(L)` քերականությունը `<T,N,R,S>` քառյակն է, որտեղ `T`֊ն տերմինալային սիմվոլների բազմությունն է, `N`֊ը՝ ոչ տերմինալային սիմվոլներինը, `R` քերականական կանոնների (կամ հավասարումների) բազմությունն է և `S`֊ն էլ սկզբնական սիմվոլն է։_

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


