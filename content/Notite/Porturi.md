
### Bit operations

| SETARE PE | INSTRUCTIUNE | OPERATOR |
| --------- | ------------ | -------- |
| 1         | p \|= (1<<)  | \|       |
| 0         | p &= ~(1<<)  | & si ~   |

### DDRRx

=> Data direction register

| Valoare | Directie |
| ------- | -------- |
| 0       | INTRARE  |
| 1       | IESIRE   |

### PORTx

| Stare pin | ce face          |
| --------- | ---------------- |
| Intrare   | seteaza PULL-UP  |
| Iesire    | Seteaza valoarea |

### PINx

read only
citeste stearea pinului (iesirea)