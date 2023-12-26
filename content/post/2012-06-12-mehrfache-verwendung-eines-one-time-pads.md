---
title: Mehrfache Verwendung eines One-Time-Pads
author: Andy
type: post
showtoc: false
date: 2012-06-12T21:03:42+00:00
url: /post/mehrfache-verwendung-eines-one-time-pads/
categories:
  - Information Security
tags:
  - cryptography

---
In diesem Artikel soll dargestellt werden, wie sich die mehrfache Verwendung desselben Schl&uuml;ssels auf die Sicherheit eines One-Time-Pads auswirkt.

#### Das One-Time-Pad

Das One-Time-Pad (OTP) ist ein einfaches Verschl&uuml;sselungsverfahren, das perfekte Sicherheit bietet und nicht gebrochen werden kann, auch nicht mit der gesamten weltweiten Rechenkraft. Perfekte Sicherheit bedeutet, dass der Geheimtext keine zus&auml;tzlichen Informationen &uuml;ber den Klartext liefert. (Es erf&uuml;llt auch das Shannon Theorem, das besagt, dass der Schl&uuml;sselraum gr&ouml;&szlig;er oder gleich dem Nachrichtenraum sein muss.)

Der Schl&uuml;ssel beim One-Time-Pad weist die gleiche L&auml;nge wie der Klartext auf. Der Schl&uuml;ssel muss absolut zuf&auml;llig generiert worden sein (normale Zufallszahlengeneratoren sind hierf&uuml;r nicht ausreichend). Der Schl&uuml;ssel muss geheim bleiben (nur Sender und Empf&auml;nger d&uuml;rfen ihn kennen) und darf nie (auch nicht teilweise) wiederverwendet werden. Nach dem Einsatz muss der Schl&uuml;ssel sicher vernichtet werden.

Beim Verschl&uuml;sseln wird der Klartext mit dem Schl&uuml;ssel (der die gleiche L&auml;nge wie der Klartext besitzt) Exklusiv-Oder-verkn&uuml;pft (XOR) und ergibt den Geheimtext. Dieser Geheimtext wird dem Empf&auml;nger &uuml;bermittelt. Zum Entschl&uuml;sseln nimmt der Empf&auml;nger den erhaltenen Geheimtext und verkn&uuml;pft ihn per <a href="http://de.wikipedia.org/wiki/Kontravalenz" target="_blank">Exklusiv-Oder (XOR)</a> mit dem Schl&uuml;ssel. Der Empf&auml;nger erh&auml;lt so den Klartext.

#### Mehrfache Verwendung des Schl&uuml;ssels

Im nachfolgenden Beispiel wird gezeigt, wie sich die mehrfache Verwendung eines Schl&uuml;ssels katastrophal auf die Sicherheit auswirkt und wie so eine Verschl&uuml;sselung gebrochen werden kann. Dabei ist der Klartext der 14 Nachrichten ein um Umlaute reduzierter Auszug aus&nbsp;_Faust I_ von Johann Wolfgang von Goethe:

<ol style="font-family: 'Courier New', Monospace; margin-left: 3em;">
  <li>
    So gib mir auch die Zeiten wieder,
  </li>
  <li>
    Da ich noch selbst im Werden war,
  </li>
  <li>
    Da sich ein Quell gedrangter Lieder
  </li>
  <li>
    Ununterbrochen neu gebar,
  </li>
  <li>
    Da Nebel mir die Welt verhullten,
  </li>
  <li>
    Die Knospe Wunder noch versprach,
  </li>
  <li>
    Da ich die tausend Blumen brach,
  </li>
  <li>
    Die alle Taler reichlich fullten.
  </li>
  <li>
    Ich hatte nichts und doch genug:
  </li>
  <li>
    Den Drang nach Wahrheit und die Lust am Trug.
  </li>
  <li>
    Gib ungebandigt jene Triebe,
  </li>
  <li>
    Das tiefe, schmerzenvolle Gluck,
  </li>
  <li>
    Des Hasses Kraft, die Macht der Liebe,
  </li>
  <li>
    Gib meine Jugend mir zuruck!
  </li>
</ol>

Jede Zeile ist dabei eine unabh&auml;ngige Nachricht, die jeweils mit demselben Schl&uuml;ssel codiert wurde.

Dies ist der Schl&uuml;ssel in Hexadezimaldarstellung (er ist 45 Bytes lang, genauso lang wie Nachricht 10, die l&auml;ngste der 14 Nachrichten):

`07 05 B6 2E E1 D4 7F 89 8F D7 02 C1 64 BD E6 FF ED 77 6C 1E E2 07 8E 77 E8 91 59 A9 FE 68 51 95 BF F3 13 4E 0A A1 B0 1E 89 FA 14 11 F7`

Zur Verschl&uuml;sselung werden alle 14 Nachrichten mit dem gleichen Schl&uuml;ssel Exklusiv-Oder-verkn&uuml;pft (XOR, &oplus;): c<sub>i</sub> = m<sub>i</sub> &oplus; k, f&uuml;r i = 1, 2, &hellip;, 14. _c<sub>i</sub>_ ist der Geheimtext mit der Nummer i, _m<sub>i</sub>_ ist der Klartext mit der Nummer i, _k_ ist der Schl&uuml;ssel.

Das Ergebnis sind folgende Geheimtexte:

<ol style="font-family: 'Courier New', Monospace; margin-left: 3em;">
  <li>
    54 6A 96 49 88 B6 5F E4 E6 A5 22 A0 11 DE 8E DF 89 1E 09 3E B8 62 E7 03 8D FF 79 DE 97 0D 35 F0 CD DF
  </li>
  <li>
    <span style="color: #f00;">43 64 96</span> 47 82 BC 5F E7 E0 B4 6A E1 17 D8 8A 9D 9E 03 4C 77 8F 27 D9 12 9A F5 3C C7 DE 1F 30 E7 93
  </li>
  <li>
    <span style="color: #f00;">43 64 96</span> 5D 88 B7 17 A9 <span style="color: #00f;">EA</span> BE 6C E1 35 C8 83 93 81 57 0B 7B 86 75 EF 19 8F E5 3C DB DE 24 38 F0 DB 96 61
  </li>
  <li>
    52 6B C3 40 95 B1 0D EB FD B8 61 A9 01 D3 C6 91 88 02 4C 79 87 65 EF 05 C4
  </li>
  <li>
    <span style="color: #f00;">43 64 96</span> 60 84 B6 1A E5 AF BA 6B B3 44 D9 8F 9A CD 20 09 72 96 27 F8 12 9A F9 <span style="color: #f0f;">2C C5 92 1C 34 FB</span> 93
  </li>
  <li>
    43 6C D3 <span style="color: #0f0;">0E</span> AA BA 10 FA FF B2 22 96 11 D3 82 9A 9F 57 02 71 81 6F AE 01 8D E3 2A D9 8C 09 32 FD 93
  </li>
  <li>
    <span style="color: #f00;">43 64 96</span> 47 82 BC 5F ED E6 B2 22 B5 05 C8 95 9A 83 13 4C 5C 8E 72 E3 12 86 B1 3B DB 9F 0B 39 B9
  </li>
  <li>
    43 6C D3 <span style="color: #0f0;">0E</span> 80 B8 13 EC AF 83 63 AD 01 CF C6 8D 88 1E 0F 76 8E 6E ED 1F C8 F7 <span style="color: #f0f;">2C C5 92 1C 34 FB</span> 91
  </li>
  <li>
    4E 66 DE <span style="color: #0f0;">0E</span> 89 B5 0B FD <span style="color: #00f;">EA</span> F7 6C A8 07 D5 92 8C CD 02 02 7A C2 63 E1 14 80 B1 3E CC 90 1D 36 AF
  </li>
  <li>
    43 60 D8 <span style="color: #0f0;">0E</span> A5 A6 1E E7 E8 F7 6C A0 07 D5 C6 A8 8C 1F 1E 76 87 6E FA 57 9D FF 3D 89 9A 01 34 B5 F3 86 60 3A 2A C0 DD 3E DD 88 61 76 D9
  </li>
  <li>
    40 6C D4 <span style="color: #0f0;">0E</span> 94 BA 18 EC ED B6 6C A5 0D DA 92 DF 87 12 02 7B C2 53 FC 1E 8D F3 3C 85
  </li>
  <li>
    43 64 C5 <span style="color: #0f0;">0E</span> 95 BD 1A EF <span style="color: #00f;">EA</span> FB 22 B2 07 D5 8B 9A 9F 0D 09 70 94 68 E2 1B 8D B1 1E C5 8B B 3A B9
  </li>
  <li>
    43 60 C5 <span style="color: #0f0;">0E</span> A9 B5 0C FA <span style="color: #00f;">EA</span> A4 22 8A 16 DC 80 8B C1 57 08 77 87 27 C3 16 8B F9 2D 89 9A 0D 23 B5 F3 9A 76 2C 6F 8D
  </li>
  <li>
    40 6C D4 <span style="color: #0f0;">0E</span> 8C B1 16 E7 <span style="color: #00f;">EA</span> F7 48 B4 03 D8 88 9B CD 1A 05 6C C2 7D FB 5 9D F2 32 88
  </li>
</ol>

Schon ein erster kurzer Blick auf die 14 Geheimtexte verr&auml;t einem Kryptoanalysten zahlreiche Informationen.

<span style="color: #f00;">rot</span>: Hier handelt es sich viermal um dasselbe Wort oder den selben Wortteil. Da das Wort am Satzanfang steht, ist das erste Zeichen wahrscheinlich ein Gro&szlig;buchstabe, vermutlich ein _D_ oder _S_. Jedoch folgen nach dem Wort insgesamt drei verschiedene Zeichen (47, 5D, 60), so dass das Zeichen 96 vermutlich ein Leerzeichen ist.  
<span style="color: #0f0;">gr&uuml;n</span>: In 8 von 14 S&auml;tzen erscheint dieses Zeichen an dieser Stelle. H&ouml;chstwahrscheinlich handelt es sich um ein Leerzeichen oder den Buchstaben _e_ (siehe Liste der <a href="http://de.wikipedia.org/wiki/Buchstabenh%C3%A4ufigkeit" target="_blank">h&auml;ufigsten Buchstaben</a>).  
<span style="color: #00f;">blau</span>: In 5 von 14 S&auml;tzen erscheint dieses Zeichen an dieser Stelle. H&ouml;chstwahrscheinlich handelt es sich um ein Leerzeichen oder den Buchstaben _e_ (siehe Liste der <a href="http://de.wikipedia.org/wiki/Buchstabenh%C3%A4ufigkeit" target="_blank">h&auml;ufigsten Buchstaben</a>).  
<span style="color: #f0f;">rosa</span>: Hier handelt es sich um dasselbe Wort oder den selben Wortteil.

Weiters kann man davon ausgehen, dass am Beginn einer Nachricht immer ein Gro&szlig;buchstabe und am Ende einer Nachricht ein Satzzeichen steht.

In Phase 2 der Kryptoanalyse, macht sich der Angreifer die mathematische Tatsache zu Nutze, dass c<sub>i</sub> &oplus; c<sub>j</sub> = (m<sub>i</sub> &oplus; k) &oplus; (m<sub>j</sub> &oplus; k) = (m<sub>i</sub> &oplus; m<sub>j</sub>) &oplus; (k &oplus; k) = m<sub>i</sub> &oplus; m<sub>j</sub>.

Beispielsweise nimmt er das <span style="color: #0f0;">0E</span>, von dem er annimmt, dass es sich um ein Leerzeichen handelt, und XOR-verkn&uuml;pft es mit 49 aus dem ersten Geheimtext an der gleichen Position 4. 0E &oplus; 49 = 47. Der <a href="http://de.wikipedia.org/wiki/American_Standard_Code_for_Information_Interchange" target="_blank">ASCII-Code</a> f&uuml;r das Leerzeichen ist 20 und dieses XOR-verkn&uuml;pft er mit 47: 47 &oplus; 20 = 67. 67 ist der ASCII-Code f&uuml;r das Zeichen _g_, was auch korrekt ist, wenn wir im Original-Klartext weiter oben nachsehen. Diesen Vorgang wiederholt er f&uuml;r die anderen Zeichen.

Auch kann der Angreifer versuchen, analog wie oben statt nur eines Zeichens die <a href="http://de.wikipedia.org/wiki/Liste_der_h%C3%A4ufigsten_W%C3%B6rter_der_deutschen_Sprache" target="_blank">h&auml;ufigsten W&ouml;rter</a> der deutschen Sprache an beliebiger Stelle einzusetzen und dann zu pr&uuml;fen, ob der andere Geheimtext an derselben Stelle einen Sinn ergibt. Falls nicht, r&uuml;ckt er eine Position weiter und versucht dasselbe erneut. Zum Beispiel die Artikel &rdquo; der &ldquo;, &rdquo; die &rdquo; und &rdquo; das &rdquo; haben eine L&auml;nge von f&uuml;nf Zeichen (jeweils mit Leerzeichen vor und nach dem Wort). Wenn er die korrekte Position eines Artikels findet, erh&auml;lt er gleich f&uuml;nf Zeichen des anderen Klartextes. Damit kann er oftmals weitere Teile vor und nach dieser Zeichengruppe erraten. Diese neuen Zeichen kann er nun wiederum mit anderen Geheimtexten per Exklusiv-Oder verkn&uuml;pfen und erh&auml;lt dann wieder neue Zeichen oder Zeichengruppen. Es ist nur eine Frage der Zeit, bis der Angreifer alle 14 Nachrichten im Klartext in seinen H&auml;nden h&auml;lt.

Bereits an diesem sehr einfachen Beispiel kann man sehen, dass die Kryptoanalyse ein iterativer und langwieriger Prozess ist, der viel Genauigkeit und Flei&szlig; erfordert.

#### In der Praxis

Dieses Beispiel sollte in erster Linie verdeutlichen, dass der Schl&uuml;ssel eines One-Time-Pads niemals (auch nicht teilweise!) wiederverwendet werden darf. Der Schl&uuml;ssel muss sofort nach der Verwendung sicher und spurlos vernichtet werden. Wird diese Regel eingehalten und wird der Schl&uuml;ssel absolut zuf&auml;llig erzeugt, dann bietet das One-Time-Pad perfekte Sicherheit und kann nicht gebrochen werden.

In der Vergangenheit wurde aber genau dieser Fehler bereits mehrfach begangen. Ein bekanntes Beispiel sind die <a href="http://de.wikipedia.org/wiki/Lorenz-Schl%C3%BCsselmaschine" target="_blank">Lorenz-Verschl&uuml;sselungsmaschinen</a> aus dem Zweiten Weltkrieg. Hier wurde von den Deutschen eine verschl&uuml;sselte Nachricht gesendet, die beim Empf&auml;nger jedoch nicht fehlerfrei angekommen ist, und sie wurde ein zweites Mal mit demselben Schl&uuml;ssel chiffriert versendet. Jedoch hat der Schreiber aus Bequemlichkeit beim zweiten Mal einige Abk&uuml;rzungen verwendet (z.B. Nr. statt Nummer) und dadurch wurde der Text ver&auml;ndert. Die Briten konnten beide Geheimtexte abfangen und anhand dieser Texte den Chiffriercode knacken und die Maschine sogar nachbauen.

Ein weiteres bekanntes historisches Beispiel ist das <a href="http://de.wikipedia.org/wiki/Venona" target="_blank">Venona-Projekt</a> aus dem Kalten Krieg.