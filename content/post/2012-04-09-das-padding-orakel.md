---
title: Das Padding-Orakel
author: Andy
type: post
showtoc: false
date: 2012-04-09T20:58:01+00:00
url: /post/das-padding-orakel/
categories:
  - Information Security
tags:
  - cryptography

---
Das Padding-Orakel wird f&uuml;r einen Seitenkanalangriff verwendet, bei dem der Geheimtext vollst&auml;ndig entschl&uuml;sselt werden kann. Funktionsweise, Angriff und Abwehr werden in diesem Artikel anhand eines konkreten Beispiels verst&auml;ndlich dargestellt.

#### Was ist Padding?

Blockchiffre-Verfahren wie AES und 3DES arbeiten mit einer bestimmen Blockgr&ouml;&szlig;e. Beispielsweise betr&auml;gt die Blockgr&ouml;&szlig;e bei AES 128 Bit und bei 3DES 64 Bit. Zu verschl&uuml;sselnde Nachrichten werden dabei in mehrere Bl&ouml;cke aufgeteilt und jeder Block wird dann einzeln verschl&uuml;sselt. Wenn der letzte Block einer Nachricht nun k&uuml;rzer ist als die jeweilige Blockgr&ouml;&szlig;e des Chiffrierers, so muss die L&uuml;cke aufgef&uuml;llt werden, damit der gesamte Block verschl&uuml;sselt werden kann. Dieses Auff&uuml;llen nennt man _Padding_. Auch wenn eine Nachricht oder der letzte Block einer Nachricht genauso lang ist wie die Blockgr&ouml;&szlig;e, findet ein Padding statt. Es wird dann einfach ein neuer vollst&auml;ndiger Block an die Nachricht als Padding angeh&auml;ngt. Ein Padding findet also unabh&auml;ngig von der Nachrichtenl&auml;nge grunds&auml;tzlich immer statt. (Es gibt Methoden wie _Ciphertext Stealing_ und den <a title="Counter Mode" href="http://http://de.wikipedia.org/wiki/Counter_Mode" target="_blank">CTR-Modus</a> von Blockchiffrierern, die ein Padding &uuml;berfl&uuml;ssig machen. Die meisten Verschl&uuml;sselungsanwendungen in der Praxis arbeiten jedoch im <a title="Cipher Block Chaining Modus" href="http://de.wikipedia.org/wiki/Cipher_Block_Chaining_Mode" target="_blank">CBC-Modus</a> mit Padding wie in diesem Artikel beschrieben.)

#### Welche Padding-Methoden gibt es?

Beim Padding nach dem _ANSI X.923_ Standard werden die fehlenden Bytes mit Nullwerten (0) aufgef&uuml;llt und das letzte Byte enth&auml;lt die Gesamtl&auml;nge des Paddings. Bei einer Blockl&auml;nge von acht Bytes (64 Bits) ergibt sich folgendes Beispiel, wenn der letzte Block mit vier Bytes aufgef&uuml;llt werden muss:

`| 90 72 94 DF <span style="color: #f00;">00 00 00 04</span> |`

Der _ISO 10126_ Standard sieht vor, dass die fehlenden Bytes mit Zufallswerten aufgef&uuml;llt werden sollen und das letzte Byte die Gesamtl&auml;nge des Paddings angeben soll. In unserem Beispiel mit vier Bytes als Padding sieht das dann so aus:

`| 90 72 94 DF <span style="color: #f00;">8F 9B 62 04</span> |`

Beim Padding nach dem _PKCS7_ Standard werden alle fehlenden Bytes mit der Gesamtl&auml;nge des Paddings aufgef&uuml;llt. D.h., wenn sieben Bytes fehlen, werden diese Bytes mit dem Wert 7 aufgef&uuml;llt; wenn drei Bytes fehlen, mit dem Wert 3 usw. In unserem Beispiel daher:

`| 90 72 94 DF <span style="color: #f00;">04 04 04 04</span> |`

#### Der Angriff

Bei der folgenden Darstellung des Angriffs mittels des Padding-Orakels sind Kenntnisse &uuml;ber die logische <a title="XOR" href="http://de.wikipedia.org/wiki/Xor" target="_blank">XOR</a>-Funktion (Exklusives Oder, Symbol: &oplus;) und die Funktionsweise eines <a title="Cipher Block Chaining Mode" href="http://de.wikipedia.org/wiki/Cipher_Block_Chaining_Mode" target="_blank">CBC-Blockchiffrierers</a> zum Verst&auml;ndnis erforderlich.

Bei dem Angriff mittels des Padding-Orakels&nbsp;handelt es sich um einen sogenannten Chosen Ciphertext Attack. Zum Gelingen ben&ouml;tigt dieser Angriff auf der Gegenseite einen Service, der eine (direkte oder indirekte) R&uuml;ckmeldung gibt, ob das Padding des verschl&uuml;sselten Geheimtextes korrekt war oder nicht. Bei der Gegenseite kann es sich beispielsweise um einen Internet- oder lokalen Netzwerk-Server handeln, der verschl&uuml;sselte Anfragen bearbeiten soll. &nbsp;Dass diese Annahmen nicht realit&auml;tsfremd sind, werden wir im letzten Abschnitt dieses Artikels sehen.

Die Funktionsweise eines CBC-Blockchiffrierers ist in der folgenden Abbildung dargestellt (Blockgr&ouml;&szlig;e acht Bytes, die zu verschl&uuml;sselnde Nachricht inkl. Padding wurde auf drei Bl&ouml;cke aufgeteilt):

<p style="text-align: center;">
  <img loading="lazy" decoding="async" class="aligncenter" title="CBC Verschl&uuml;sselung" src="http://upload.wikimedia.org/wikipedia/commons/d/d3/Cbc_encryption.png" alt="CBC Verschl&uuml;sselung" width="600" height="243" />
</p>

Wichtig ist zu erkennen, dass jeder Klartextblock (Plaintext) vor dem Verschl&uuml;sseln mit dem Initialisierungsvektor (IV) bzw. dem im vorherigen Schritt erzeugten Geheimtextblock (Ciphertext) per XOR verkn&uuml;pft wird.

Der Angreifer ist im Besitz eines verschl&uuml;sselten Geheimtextes (einschlie&szlig;lich des zuf&auml;llig erstellten IV, der in der Regel mitgesendet wird) und m&ouml;chte die Nachricht entschl&uuml;sseln.

C<sub>1</sub>, C<sub>2</sub>, C<sub>3</sub> sind die drei Geheimtextbl&ouml;cke der verschl&uuml;sselten Nachricht und sind dem Angreifer bekannt; M<sub>1</sub>, M<sub>2</sub>, M<sub>3</sub> die drei Klartextbl&ouml;cke der Nachricht und sind geheim. Wie wir aus obiger Abbildung erkennen k&ouml;nnen, gilt C<sub>2</sub> = E(C<sub>1</sub> &oplus;&nbsp;M<sub>2</sub>), wobei E die Verschl&uuml;sselungsfunktion darstellt. Beispiel:

`| 7D 3E 47 50 64 7A 20 65 | = M<sub>2</sub>`  
`| 3A 80 4D 2A 88 5F 3D 9E | = C<sub>1</sub>`  
`| 47 BE 0A 7A EC 25 1D FB | = C<sub>1</sub> ⊕ M<sub>2</sub>`  
`| 73 9C 1F 2A DA 1D 2E AA | = C<sub>2</sub> = E(C<sub>1</sub> ⊕ M<sub>2</sub>)`

In unserem Beispiel arbeitet das Verschl&uuml;sselungssystem mit Padding nach dem PKCS7-Standard, d.h. g&uuml;ltige Paddings w&auml;ren 01 oder 02 02 oder 03 03 03 usw.

Der Angreifer m&ouml;chte nun M<sub>2</sub> in Erfahrung bringen. Der Angreifer arbeitet sich bei diesem Angriff immer vom letzten Byte zum ersten vor. Da das h&auml;ufigste Zeichen in deutschen Texten das Leerzeichen ist, wird das Leerzeichen (ASCII-Code 20) als erste Annahme f&uuml;r das letzte Byte von M<sub>2</sub> verwendet. Der Angreifer ersetzt das letzte Byte in C<sub>1</sub> (n&auml;mlich 9E) durch das Ergebnis von 9E &oplus; 20 &oplus; 01 = BF. Der Wert 20 steht dabei f&uuml;r das Leerzeichen und der Wert 01 f&uuml;r ein Padding mit der L&auml;nge 1.

Aus  
`| 3A 80 4D 2A 88 5F 3D 9E | = C<sub>1</sub>`  
wird  
`| 3A 80 4D 2A 88 5F 3D <span style="color: #f00;">BF</span> | = C'<sub>1</sub>`

Der Angreifer schickt dann die drei Bl&ouml;cke IV, C&rsquo;<sub>1</sub>, C<sub>2</sub> an den Server, der den manipulierten Geheimtext entschl&uuml;sselt, das Padding auf G&uuml;ltigkeit &uuml;berpr&uuml;ft und abh&auml;ngig davon eine Akzeptanz- oder Fehlermeldung an den Angreifer zur&uuml;ckgibt. Wie wir aus obiger Abbildung erkennen k&ouml;nnen, gilt M<sub>2</sub> = D(C<sub>2</sub>) &oplus;&nbsp;C<sub>1</sub>), wobei D die Entschl&uuml;sselungsfunktion darstellt. Der Server entschl&uuml;sselt C<sub>2</sub> und erh&auml;lt f&uuml;r das letzte Byte von C<sub>2</sub> (AA) den Wert FB (siehe oben). FB wird nun mit dem letzten Byte von C&rsquo;<sub>1</sub> per Xor verkn&uuml;pft und ergibt FB &oplus; BF = 44. Dies ist kein g&uuml;ltiger Padding-Wert, da der Wert gr&ouml;&szlig;er ist als die Blockl&auml;nge. Der Server gibt also eine Fehlermeldung zur&uuml;ck, das Padding ist ung&uuml;ltig.

In unserem Beispiel hatte der Angreifer Pech, denn das letzte Byte von M<sub>2</sub> besitzt tats&auml;chlich den Wert 65 (siehe oben) und nicht 20 wie vermutet. Als n&auml;chstes probiert es der Angreifer mit dem zweith&auml;ufigsten Zeichen, dem Buchstaben _e_. Der ASCII-Code f&uuml;r e lautet 65. Der Angreifer ersetzt das letzte Byte in C<sub>1</sub> durch das Ergebnis von 9E &oplus; 65 &oplus; 01 = FA.

Aus  
`| 3A 80 4D 2A 88 5F 3D 9E | = C<sub>1</sub>`  
wird  
`| 3A 80 4D 2A 88 5F 3D <span style="color: #f00;">FA</span> | = C'<sub>1</sub>`

Wiederum sendet der Angreifer die drei Bl&ouml;cke IV, C&rsquo;<sub>1</sub>, C<sub>2</sub> an den Server, der den manipulierten Geheimtext entschl&uuml;sselt, das Padding auf G&uuml;ltigkeit &uuml;berpr&uuml;ft und abh&auml;ngig davon eine Akzeptanz- oder Fehlermeldung an den Angreifer zur&uuml;ckgibt. Der Server entschl&uuml;sselt C<sub>2</sub> und erh&auml;lt f&uuml;r das letzte Byte von C<sub>2</sub> (AA) den Wert FB (siehe oben). FB wird nun mit dem letzten Byte von C&rsquo;<sub>1</sub> per Xor verkn&uuml;pft und ergibt FB &oplus; FA = 01. Dies ist ein g&uuml;ltiger Padding-Wert, n&auml;mlich ein Padding der L&auml;nge 1, genauso wie es der Angreifer beabsichtigt hatte. Der Server antwortet also, dass das Padding g&uuml;ltig war und der Geheimtext akzeptiert wurde.

Dieses Mal hatte der Angreifer Gl&uuml;ck, das letzte Byte von M<sub>2</sub> hat tats&auml;chlich den Wert 65. Der Angreifer wei&szlig; nun, dass das letzte Byte in M<sub>2</sub> den Wert 65 (d.h., Buchstabe e) hat!

H&auml;tte der Angreifer diesmal auch keinen Erfolg gehabt, h&auml;tte er alle m&ouml;glichen Werte f&uuml;r das letzte Byte bis zum Erfolg durchprobiert (insgesamt 256 m&ouml;gliche Werte, 00 bis FF).

Da der Angreifer nun das letzte Byte der Nachricht M<sub>2</sub> kennt, widmet er sich dem vorletzten Byte. Der Angreifer ersetzt das vorletzte Byte in C<sub>1</sub> durch das Ergebnis von 3D &oplus; 20 &oplus; 02 = 1F. Das letzte Byte wird durch 9E &oplus; 65 &oplus; 02 = F9. Die beiden Werte 02 stehen f&uuml;r das Padding mit der L&auml;nge 2.

Aus  
`| 3A 80 4D 2A 88 5F 3D 9E | = C<sub>1</sub>`  
wird  
`| 3A 80 4D 2A 88 5F <span style="color: #f00;">1F F9</span> | = C'<sub>1</sub>`

Dieser Vorgang wird solange wiederholt, bis der Angreifer auch den korrekten Wert f&uuml;r das vorletzte Byte kennt. Dann geht er &uuml;ber zum drittletzten Byte usw., bis er schlie&szlig;lich auch das erste Byte von M<sub>2</sub> ermitteln konnte. Der Angreifer kennt damit alle Bytes des Blocks M<sub>2</sub>. Diese Vorgehensweise wiederholt er nun mit den beiden restlichen Bl&ouml;cken M<sub>1</sub> (mit Hilfe des bekannten IV) und M<sub>3</sub> der Nachricht und kann so in relativ kurzer Zeit den kompletten Geheimtext entschl&uuml;sseln.

Sollte der IV dem Angreifer nicht bekannt sein, so kann er trotzdem alle Bl&ouml;cke bis auf den ersten entschl&uuml;sseln.

(Eine alternative Vorgehensweise besteht darin, die Werte nicht nach einer H&auml;ufigkeitsverteilung sondern zuf&auml;llig auszuw&auml;hlen. Die Wahrscheinlichkeit, dass dann der erste Versuch richtig ist, betr&auml;gt 1/256. Im Durchschnitt wird der Angreifer pro zu erratendem Byte 256/2 = 128 Versuche ben&ouml;tigen. Bei einer Blockl&auml;nge von 8 Bytes ergeben das durchschnittlich 8*128 = 1.024 Versuche pro Block. Dieser Angriff ist folglich sehr effizient.)

#### Das Padding-Orakel in der Praxis

Nachdem wir nun in der Theorie und anhand eines Beispiels verstanden haben, wie der Angriff funktioniert, stellt sich die Frage, ob er denn auch in der Praxis m&ouml;glich ist bzw. schon durchgef&uuml;hrt wurde. Die Antwort lautet ja. Es gibt sogar im Internet ein Tool, das genau so einen Angriff durchf&uuml;hrt.

Beim Padding-Orakel handelt es sich um eine <a title="Side Channel Attack" href="http://de.wikipedia.org/wiki/Side_channel_attack" target="_blank">Seitenkanalattacke</a> (Side Channel Attack). Der Erfolg dieses Angriffs h&auml;ngt davon ab, ob die entschl&uuml;sselnde Instanz (z.B. ein Internet-Server) in direkter oder indirekter Form eine R&uuml;ckmeldung &uuml;ber die G&uuml;ltigkeit des Paddings zur&uuml;ckgibt. Die Internet-Protokolle SSL/TLS und IPSec sind grunds&auml;tzlich empf&auml;nglich f&uuml;r diesen Angriff. Ein sehr sch&ouml;nes Beispiel eines Timing Attacks, der das Padding-Orakel benutzt, gibt Brice Canvel in <a href="http://lasecwww.epfl.ch/memo/memo_ssl.shtml" target="_blank">Password Interception in a SSL/TLS Channel</a>.

Wie k&ouml;nnen Angriffe dieser Form verhindert werden? Durch Verwendung des CTR- statt des CBC-Modus bei Blockchiffrierern (im CTR-Modus findet kein Padding statt) oder durch das Erstellen und Mitsenden eines <a href="http://de.wikipedia.org/wiki/Message_Authentication_Code" target="_blank">Message Authentication Codes</a> (MAC) von der verschl&uuml;sselten Nachricht. Ebenfalls sollten entschl&uuml;sselnde Instanzen keine R&uuml;ckmeldungen &uuml;ber Erfolg oder Fehler eines Entschl&uuml;sselungsvorganges geben, was allerdings in der Realit&auml;t nicht immer m&ouml;glich sein wird.

Weiterf&uuml;hrende Literatur:  
Serge Vaudenay: <a href="http://www.iacr.org/archive/eurocrypt2002/23320530/cbc02_e02d.pdf" target="_blank">Security Flaws Induced by CBC Padding Applications to SSL, IPSEC, WTLS&hellip;</a>