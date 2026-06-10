
### Descriere

Frazele reprezintă grupuri de propoziţii între care există corelaţii (de coordonare sau subordonare) şi care pot fi privite sintactic şi semantic unitar/autonom

Propoziţiile reprezintă cele mai mici entităţi sintactice (semi)autonome adică reprezintă combinaţii lexicale cu anumite semnificaţii sau corelări/grupări ale unor expresii.

## In programare

in cazul limbajelor de programare propoziţiilor sau frazelor le corespund instrucţiunile simple sau complexe (compuse/corelate), formate din combinaţii de expresii evaluabile de un număr de ori, condiţionat unele funcţie de altele.

preprocesare (procesare text), procesare/codare, atribuire, intrare/ieşire, control (transfer/salt, condiţionale, ciclare) e

### Blocurile multi-instrucţiune

blocurile multi-instrucţiune reprezintă un grup de propoziţii/instrucţiuni între care există corelaţii 
(de coordonare, subordonare sau incluziune):

- coordonare:  v\[a] = x; a++ 
- subordonare: flow control: if , switch etc
- incluziune: nested blocks 

Implementarea instrucţiunilor presupune utilizarea unor repere de execuţie sau de salt (etichete sau label-uri) prin intermediul cărora se execută selectiv/condiţionat anumite expresii sau (sub)instrucţiuni (statements).

De exemplu, instrucţiunea if Condition then StatementIf else StatementElse endif se implementează şi se interpretează/execută de către sistemele de calcul conform descrierii formale

```c
NEW LabelElse, LabelEndIf 

IF !Condition THEN 
    GOTO LabelElse 

    StatementIf 
    GOTO LabelEndIf 

LabelElse: 
    StatementElse 

LabelEndIf: 

OLD LabelEndIf, LabelElse
```

`NEW` și `OLD` reprezintă instrucțiuni speciale utilizate pentru gestionarea memoriei și generarea dinamică de etichete (labels) în cadrul unui translator.



