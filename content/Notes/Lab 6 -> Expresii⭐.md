
[[LFT_Lab_all_in_one.pdf#page=11&selection=9,0,9,8|LFT_Lab_all_in_one, page 11]]
### Descriere

Expresiile reprezintă combinaţii de literali (operatori/operanzi: cuvinte, variabile, constante, separatori) ce descriu informaţii corelate sau procese de calcul care prin evaluare exprimă cantităţi echivalente acestora

Procesul de evaluare poate să producă si efecte secundare (side effects) dacă se modifică şi parametrii externi expresiei 
(precum în `C+=(A++)+B|9`).


 Procesarea expresiilor presupune identificarea operaţiilor (de prefixare, postfixare sau infixare) şi pe baza precedenţelor / priorităţilor acestora şi a tipului de asociativitate se gestionează logic elementele expresiei prin combinarea cel mult a două cantităţi.

### Clasificare

Funcţie de aritatea operaţiilor, expresiile pot conţine operaţii unare, binare, trinare (/ternare idk de unde a luat cu trinare)

Funcţie de tipul datelor asupra cărora operează, expresiile pot fi aritmetice, logice, ordinale etc.


Funcţie de elementele asupra cărora operează, expresiile pot 
	- produce modificări ale unor variabile (incrementare, atribuire, multiplicare etc.)  pot valida  
	- extrage anumite componente (expresii regulate).


### Expresii regulate (regex)

Expresiile regulate (RegEx/RegExp - Regular Expression) reprezintă paternuri/şabloane formate din succesiuni de simboluri şi reguli (meta-caractere) ce identifică, extrag sau înlocuiesc secvenţe de text


Standardizarea POSIX (Portable Operating System Interface) a expresiilor regulate cuprinde SRE/BRE/ ERE (Simple/Basic/Extended Regular Expressions) în care unele dintre regulile-simbol de bază sunt completate/înlocuite cu [[LFT_Lab_all_in_one.pdf#page=12&selection=371,1,477,5|LFT_Lab_all_in_one, page 12]]
