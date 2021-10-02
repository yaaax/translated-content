---
title: Utiliser XMLHttpRequest
slug: Web/API/XMLHttpRequest/Using_XMLHttpRequest
tags:
  - AJAX
  - File API
  - FormData
  - JXON
  - Progression
  - Téléchargement
  - XML
  - XMLHttpRequest
  - upload
translation_of: Web/API/XMLHttpRequest/Using_XMLHttpRequest
original_slug: Web/API/XMLHttpRequest/Utiliser_XMLHttpRequest
---

<p><a href="/en-US/docs/DOM/XMLHttpRequest"><code>XMLHttpRequest</code></a> permet d'envoyer des requêtes HTTP de manière très simple.  Il suffit de créer une instance de l'objet, d'ouvrir une URL, et d'envoyer la requête.  Le <a href="/en-US/docs/HTTP/HTTP_response_codes">status HTTP</a> du résultat, tout comme le contenu de la réponse, sont disponibles dans l'objet de la requête quand la transaction est terminée. Cette page présente quelques-uns des cas d'utilisation communs et même un peu obscurs pour cet objet JavaScript puissant.</p>

<pre class="brush: js">function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.onload = reqListener;
oReq.open("get", "yourFile.txt", true);
oReq.send();</pre>

<h2 id="Types_de_requêtes">Types de requêtes</h2>

<p>Une demande faite via XMLHttpRequest peut récupérer les données dans l'une des deux façons, de manière asynchrone ou synchrone. Le type de demande est dictée par l'argument optionnel async (le troisième argument) qui est mis sur la méthode <a href="/en-US/docs/DOM/XMLHttpRequest#open()">open()</a> XMLHttpRequest (). Si cet argument est true ou non spécifié, l'objet XMLHttpRequest est traitée de manière asynchrone, sinon le processus est effectué de façon synchrone. Une discussion détaillée et des démonstrations de ces deux types de demandes peuvent être trouvés sur la page des <a href="/en-US/docs/DOM/XMLHttpRequest/Synchronous_and_Asynchronous_Requests">requêtes synchrones et asynchrones</a>. En général, vous devriez rarement, voire jamais, utiliser requêtes synchrones.</p>

<div class="note">
  <p><strong>Note :</strong> A partir de Gecko 30.0 {{ geckoRelease("30.0") }}, les requêtes synchrones sur le processus principal ont été dépréciées en raison de l'effet négatif sur l'expérience utilisateur.</p>
</div>

<h2 id="Gérer_les_réponses">Gérer les réponses</h2>

<p>Il existe plusieurs types <a href="http://www.w3.org/TR/XMLHttpRequest2/#response">d'attributs de réponse</a> définies par la spécification W3C pour XMLHttpRequest. Ceci indique que le client faisant l'objet XMLHttpRequest des informations importantes sur l'état ​​de la réponse. Certains cas où, traitant de types de réponse non-texte peuvent impliquer une certaine manipulation et l'analyse comme indiqué dans les sections suivantes.</p>

<h3 id="Analyser_et_manipuler_la_propriété_responseXML">Analyser et manipuler la propriété <code>responseXML</code></h3>

<p>Si vous utlisez <code>XMLHttpRequest </code>pour obtenir le contenu d'un fichier XML distant, la propriété <code>responseXML </code>sera un Objet DOM contenant le document XML parsé, qui peut être difficile à manipuler et analyser. Il y a quatres moyens principaux d'analyser ce document XML :</p>

<ol>
 <li>Utiliser <a href="/en-US/docs/XPath">XPath</a> pour localiser des parties.</li>
 <li>Utiliser <a href="/en-US/docs/JXON">JXON</a> pour le convertir en Objet structuré JavaScript.</li>
 <li>Manuellement <a href="/en-US/docs/Parsing_and_serializing_XML">parser et serializer le XML</a> en chaînes de caractères ou en objets.</li>
 <li>Utiliser <a href="/en-US/docs/XMLSerializer">XMLSerializer</a> pour serializer <strong>le DOM en chaînes ou en fichiers.</strong></li>
 <li><a href="/en-US/docs/JavaScript/Reference/Global_Objects/RegExp">RegExp </a>peut être utlisé si vous connaissez à l'avance le contenu du document XML. Vous pouvez supprimer les sauts de ligne, si vous utlisez RegExp en prenant en compte ces sauts. Toutefois, cette méthode est un "dernier recours", car si le code XML change légèrement, la méthode échouera probablement.</li>
</ol>

<h3 id="Analyser_et_manipuler_une_propriété_responseText_contenant_un_document_HTML">Analyser et manipuler une propriété <code>responseText</code> contenant un document HTML</h3>

<div class="note">
  <p><strong>Note :</strong> La spécification W3C <a href="http://dvcs.w3.org/hg/xhr/raw-file/tip/Overview.html">XMLHttpRequest</a>  autorise le HTML a être parsé via la propritété <code>XMLHttpRequest.responseXML</code>. Lisez l'article à propos du <a href="/en-US/docs/HTML_in_XMLHttpRequest">HTML dans XMLHttpRequest</a> pour plus de détails.</p>
</div>

<p>Si vous utilisez <code>XMLHttpRequest</code> pour récupérer le contenu d'une page HTML distante, la propriété <code>responseText</code> est une chaîne de caractères contenant une "soupe" de tous les tags HTML, qui peut être difficile à manipuler et à analyser. Il y a trois moyens principaux d'analyser cette soupe de HTML :</p>

<ol>
 <li>Utiliser la propriété <code>XMLHttpRequest.responseXML</code>.</li>
 <li>Injecter le contenu dans le body d'un <a href="/en-US/docs/Web/API/DocumentFragment">fragment de document</a> via <code>fragment.body.innerHTML</code> et traverser le DOM du fragment.</li>
 <li><a href="/en-US/docs/JavaScript/Reference/Global_Objects/RegExp">RegExp </a>peut être utlisé si vous connaissez à l'avance le contenu du document HTML dans responseText. Vous pouvez supprimer les sauts de ligne, si vous utlisez RegExp en prenant en compte ces sauts. Toutefois, cette méthode est un "dernier recours", car si le code XML change légèrement, la méthode échouera probablement.</li>
</ol>

<h2 id="Gérer_les_données_binaires">Gérer les données binaires</h2>

<p>Bien que <code>XMLHttpRequest</code> est le plus souvent utilisé pour envoyer et recevoir des données textuelles, on peut l'utiliser pour envoyer et recevoir du contenu binaire.Il existe plusieurs méthodes éprouvées pour contraindre la réponse d'un XMLHttpRequest en l'envoi de données binaires. Celles-ci impliquent d'utiliser la méthode .overrideMimeType() sur l'objet XMLHttpRequest, ce qui est une solution viable.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();
oReq.open("GET", url, true);
// récupérer les données non traitées comme une chaîne binaire
oReq.overrideMimeType("text/plain; charset=x-user-defined");
/* ... */
</pre>

<p>La Spécification XMLHttpRequest Niveau 2 ajoute de nouveaux <a href="http://www.w3.org/TR/XMLHttpRequest2/#the-responsetype-attribute">attributs responseType</a> qui permettent d'envoyer et de recevoir des données binaires plus facilement.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.open("GET", url, true);
oReq.responseType = "arraybuffer";
oReq.onload = function(e) {
  var arraybuffer = oReq.response; // n'est pas responseText
  /* ... */
}
oReq.send();
</pre>

<p>Pour plus d'exemples, jettez un oeil à la page <a href="/en-US/docs/DOM/XMLHttpRequest/Sending_and_Receiving_Binary_Data">Envoyer et recevoir des données binaires</a></p>

<h2 id="Surveiller_la_progression">Surveiller la progression</h2>

<p><code>XMLHttpRequest</code> fournit la possibilité d'écouter différents évènements qui peuvent se produire pendant que la requête est traitée. Cela inclu des notifications de progression périodiques, des notifications d'erreur, ainsi de suite.</p>

<p>Le support des évènements de progression DOM de <code>XMLHttpRequest</code> suit l'API web <a href="http://dev.w3.org/2006/webapi/progress/Progress.html">de spécifications des évènements de progression</a>: ils implémentent l'interface {{domxref("ProgressEvent")}}.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.addEventListener("progress", updateProgress, false);
oReq.addEventListener("load", transferComplete, false);
oReq.addEventListener("error", transferFailed, false);
oReq.addEventListener("abort", transferCanceled, false);

oReq.open();

// ...

// progression des transferts depuis le serveur jusqu'au client (téléchargement)
function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total;
    // ...
  } else {
    // Impossible de calculer la progression puisque la taille totale est inconnue
  }
}

function transferComplete(evt) {
  alert("Le transfert est terminé.");
}

function transferFailed(evt) {
  alert("Une erreur est survenue pendant le transfert du fichier.");
}

function transferCanceled(evt) {
  alert("Le transfert a été annulé par l'utilisateur.");
}</pre>

<p>Les lignes 3-6 ajoutent des écouteurs pour les différents évènements lancés pendant la transfert de données d'une <code>XMLHttpRequest</code>.</p>

<div class="note">
  <p><strong>Note :</strong> Vous devez ajouter les écouteurs avant d'appeler <code>open()</code> sur la requête. Sinon, les évènements de progression ne seront pas lancés.</p>
</div>

<p>Le gestionnaire de progression, spécifié par la fonction <code>updateProgress()</code> dans cet exemple, reçoit le nombre total de bytes à transférer ainsi que le nombre de bytes déjà transférés dans les champs <code>total</code> et <code>loaded</code>.  Cependant, si le champ <code>lengthComputable</code> est false, la taille totale est inconnue et sera zéro.</p>

<p>Les évènements de progression sont disponibles pour l'envoi et la réception de données. Les évènements de téléchargement sont lancés sur l'objet <code>XMLHttpRequest</code> lui-même, comme montré dans l'exemple ci-dessus. Les évènements d'envoi (upload) sont lancés sur l'objet <code>XMLHttpRequest.upload</code>, comme montré ci-dessous:</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.upload.addEventListener("progress", updateProgress, false);
oReq.upload.addEventListener("load", transferComplete, false);
oReq.upload.addEventListener("error", transferFailed, false);
oReq.upload.addEventListener("abort", transferCanceled, false);

oReq.open();
</pre>

<div class="note">
  <p><strong>Note :</strong> Les évènements de progression ne sont pas disponibles sur le protocole <code>file:</code>.</p>
</div>

<div class="note">
  <p><strong>Note :</strong> Actuellement, il y a encore des bugs ouverts pour les évènements de progression qui affectent la version 25 de Firefox sur <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=908375">OS X</a> et <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=786953">Linux</a>.</p>
</div>

<div class="note">
<p><strong>Note :</strong> A partir de {{Gecko("9.0")}}, les évènements de progression peuvent désormais être assurément présents pour chaque morceau de données reçu, y compris le dernier morceau dans les cas où le dernier paquet est reçu et la connexion fermée avant que l'évènement ne soit lancé. Dans ce cas, l'évènement de progression est automatiquement lancé quand l'évènement load se produit pour ce paquet. Cela vous permet de surveiller fiablement la progression grâce à son évènement.</p>
</div>

<div class="note">
<p><strong>Note :</strong> Dans {{Gecko("12.0")}}, si votre évènement de progression est appelé avec un <code>responseType</code> "moz-blob", la valeur de la réponse est un {{domxref("Blob")}} contenant les données reçues jusqu'à présent.</p>
</div>

<p>Une fonction peut aussi être appelée peu importe le status de fin de la requête (<code>abort</code>, <code>load</code>, ou <code>error</code>) en utilisant l'évènement <code>loadend</code> :</p>

<pre class="brush:js">req.addEventListener("loadend", loadEnd, false);

function loadEnd(e) {
  alert("Le transfert est terminé (même si l'on ne sait pas si ça a fonctionné ou pas).");
}
</pre>

<p>Notez qu'il n'y a pas moyen d'être certain des informations reçues dans l'évènement  <code>loadend</code> event comme la condition qui a causé la fin de l'opération; toutefois, vous pouvez utiliser cet évènement pour gérer les tâches qui doivent être exécutées dans tous les cas une fois un transfert terminé.</p>

<h2 id="Envoyer_des_formulaires_et_uploader_des_fichiers">Envoyer des formulaires et uploader des fichiers</h2>

<p>Les instances de <code>XMLHttpRequest</code> peuvent être utilisées pour envoyer des formulaires de deux façons :</p>

<ul>
 <li>n'utiliser rien de plus qu'AJAX</li>
 <li>utiliser l'API <a href="/en-US/docs/DOM/XMLHttpRequest/FormData"><code>FormData</code></a></li>
</ul>

<p>La <strong>seconde solution</strong> (utiliser l'API <a href="/en-US/docs/DOM/XMLHttpRequest/FormData"><code>FormData</code></a>) est la plus simple et la plus rapide, mais a le désavantage que les données collectées ne peuvent pas être <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">chainifiées</a>.<br>
 La <strong>première solution</strong> est plutôt la plus complexe, mais se prête à être plus flexible et puissante.</p>

<h3 id="Rien_de_plus_que_XMLHttpRequest">Rien de plus que <code>XMLHttpRequest</code></h3>

<p>Envoyer des formulaires sans l'API <a href="/en-US/docs/DOM/XMLHttpRequest/FormData"><code>FormData</code></a> ne demande rien de plus, si ce n'est l'API <a href="/en-US/docs/DOM/FileReader"><code >FileReader</code></a>, mais seulement <strong>si vous voulez envoyer un fichier ou plus</strong>.</p>

<h4 id="Une_brève_introduction_au_méthodes_de_submission">Une brève introduction au méthodes de submission</h4>

<p>Un élément HTML {{ HTMLElement("form") }} peut être envoyé de quatre manières :</p>

<ul>
 <li>en utilisant la méthode <code>POST</code> et en configurant son attribut <code>enctype</code> sur <code>application/x-www-form-urlencoded</code> (par défaut);</li>
 <li>en utilisant la méthode <code>POST</code> et en configurant son attribut <code>enctype</code> sur <code>text/plain</code>;</li>
 <li>en utilisant la méthode <code>POST</code> et en configurant son attribut <code>enctype</code> sur <code>multipart/form-data</code>;</li>
 <li>en utilisant la méthode <code>GET</code> (dans ce cas, l'attribut <code>enctype</code> sera ignoré).</li>
</ul>

<p>Maintenant, considérons qu'on envoie un formulaire contenant seulement deux champs, nommées <code >foo</code> et <code >baz</code>. Si vous utilisez la méthode <code >POST</code>, le serveur va recevoir une chaîne similaire à l'un des trois suivantes, en fonction de l'encodage que vous utilisez :</p>

<ul>
 <li>
  <p>Méthode: <code>POST</code>; Encodage: <code>application/x-www-form-urlencoded</code> (par défaut):</p>

  <pre>Content-Type: application/x-www-form-urlencoded

foo=bar&amp;baz=The+first+line.&amp;#37;0D%0AThe+second+line.%0D%0A</pre>
 </li>
 <li>
  <p>Méthode: <code>POST</code>; Encodage: <code>text/plain</code>:</p>

  <pre>Content-Type: text/plain

foo=bar
baz=The first line.
The second line.</pre>
 </li>
 <li>
  <p>Méthode: <code>POST</code>; Encodage: <code>multipart/form-data</code>:</p>

  <pre>Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--</pre>
 </li>
</ul>

<p>Si vous utilisez la méthode <code>GET</code> à la place, une chaîne comme celle-ci sera simplement ajoutée à l'URL :</p>

<pre>?foo=bar&amp;baz=The%20first%20line.%0AThe%20second%20line.</pre>

<h4 id="Un_petit_framework_vanilla">Un petit framework vanilla</h4>

<p>Tout celà est fait automatiquement par le serveur quand vous envoyez un {{ HTMLElement("form") }}. Mais si vous voulez faire la même chose en utilisant JavaScript vous devez <em>tout</em> dire à l'interprète. Pour celà, la manière d'envoyer des formulaires en <em>pure</em> AJAX est trop compliquée pour être expliquée ici. Pour cette raison, nous avons posté un <strong>framework complet (mais tout de  même didactique)</strong>, qui est capable d'utiliser les quatres méthodes de <em>submit</em> , et aussi de <strong>transférer des fichiers</strong>:</p>


<pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;title&gt;Sending forms with pure AJAX &amp;ndash; MDN&lt;/title&gt;
&lt;script type="text/javascript"&gt;

"use strict";

/*\
|*|
|*|  :: XMLHttpRequest.prototype.sendAsBinary() Polyfill ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest#sendAsBinary()
\*/

if (!XMLHttpRequest.prototype.sendAsBinary) {
  XMLHttpRequest.prototype.sendAsBinary = function(sData) {
    var nBytes = sData.length, ui8Data = new Uint8Array(nBytes);
    for (var nIdx = 0; nIdx &lt; nBytes; nIdx++) {
      ui8Data[nIdx] = sData.charCodeAt(nIdx) &amp; 0xff;
    }
    /* send as ArrayBufferView...: */
    this.send(ui8Data);
    /* ...or as ArrayBuffer (legacy)...: this.send(ui8Data.buffer); */
  };
}

/*\
|*|
|*|  :: AJAX Form Submit Framework ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest/Using_XMLHttpRequest
|*|
|*|  This framework is released under the GNU Public License, version 3 or later.
|*|  http://www.gnu.org/licenses/gpl-3.0-standalone.html
|*|
|*|  Syntax:
|*|
|*|   AJAXSubmit(HTMLFormElement);
\*/

var AJAXSubmit = (function () {

  function ajaxSuccess () {
    /* console.log("AJAXSubmit - Success!"); */
    alert(this.responseText);
    /* you can get the serialized data through the "submittedData" custom property: */
    /* alert(JSON.stringify(this.submittedData)); */
  }

  function submitData (oData) {
    /* the AJAX request... */
    var oAjaxReq = new XMLHttpRequest();
    oAjaxReq.submittedData = oData;
    oAjaxReq.onload = ajaxSuccess;
    if (oData.technique === 0) {
      /* method is GET */
      oAjaxReq.open("get", oData.receiver.replace(/(?:\?.*)?$/, oData.segments.length &gt; 0 ? "?" + oData.segments.join("&amp;") : ""), true);
      oAjaxReq.send(null);
    } else {
      /* method is POST */
      oAjaxReq.open("post", oData.receiver, true);
      if (oData.technique === 3) {
        /* enctype is multipart/form-data */
        var sBoundary = "---------------------------" + Date.now().toString(16);
        oAjaxReq.setRequestHeader("Content-Type", "multipart\/form-data; boundary=" + sBoundary);
        oAjaxReq.sendAsBinary("--" + sBoundary + "\r\n" + oData.segments.join("--" + sBoundary + "\r\n") + "--" + sBoundary + "--\r\n");
      } else {
        /* enctype is application/x-www-form-urlencoded or text/plain */
        oAjaxReq.setRequestHeader("Content-Type", oData.contentType);
        oAjaxReq.send(oData.segments.join(oData.technique === 2 ? "\r\n" : "&amp;"));
      }
    }
  }

  function processStatus (oData) {
    if (oData.status &gt; 0) { return; }
    /* the form is now totally serialized! do something before sending it to the server... */
    /* doSomething(oData); */
    /* console.log("AJAXSubmit - The form is now serialized. Submitting..."); */
    submitData (oData);
  }

  function pushSegment (oFREvt) {
    this.owner.segments[this.segmentIdx] += oFREvt.target.result + "\r\n";
    this.owner.status--;
    processStatus(this.owner);
  }

  function plainEscape (sText) {
    /* how should I treat a text/plain form encoding? what characters are not allowed? this is what I suppose...: */
    /* "4\3\7 - Einstein said E=mc2" ----&gt; "4\\3\\7\ -\ Einstein\ said\ E\=mc2" */
    return sText.replace(/[\s\=\\]/g, "\\$&amp;");
  }

  function SubmitRequest (oTarget) {
    var nFile, sFieldType, oField, oSegmReq, oFile, bIsPost = oTarget.method.toLowerCase() === "post";
    /* console.log("AJAXSubmit - Serializing form..."); */
    this.contentType = bIsPost &amp;&amp; oTarget.enctype ? oTarget.enctype : "application\/x-www-form-urlencoded";
    this.technique = bIsPost ? this.contentType === "multipart\/form-data" ? 3 : this.contentType === "text\/plain" ? 2 : 1 : 0;
    this.receiver = oTarget.action;
    this.status = 0;
    this.segments = [];
    var fFilter = this.technique === 2 ? plainEscape : escape;
    for (var nItem = 0; nItem &lt; oTarget.elements.length; nItem++) {
      oField = oTarget.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ? oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE" &amp;&amp; oField.files.length &gt; 0) {
        if (this.technique === 3) {
          /* enctype is multipart/form-data */
          for (nFile = 0; nFile &lt; oField.files.length; nFile++) {
            oFile = oField.files[nFile];
            oSegmReq = new FileReader();
            /* (custom properties:) */
            oSegmReq.segmentIdx = this.segments.length;
            oSegmReq.owner = this;
            /* (end of custom properties) */
            oSegmReq.onload = pushSegment;
            this.segments.push("Content-Disposition: form-data; name=\"" + oField.name + "\"; filename=\""+ oFile.name + "\"\r\nContent-Type: " + oFile.type + "\r\n\r\n");
            this.status++;
            oSegmReq.readAsBinaryString(oFile);
          }
        } else {
          /* enctype is application/x-www-form-urlencoded or text/plain or method is GET: files will not be sent! */
          for (nFile = 0; nFile &lt; oField.files.length; this.segments.push(fFilter(oField.name) + "=" + fFilter(oField.files[nFile++].name)));
        }
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        /* field type is not FILE or is FILE but is empty */
        this.segments.push(
          this.technique === 3 ? /* enctype is multipart/form-data */
            "Content-Disposition: form-data; name=\"" + oField.name + "\"\r\n\r\n" + oField.value + "\r\n"
          : /* enctype is application/x-www-form-urlencoded or text/plain or method is GET */
            fFilter(oField.name) + "=" + fFilter(oField.value)
        );
      }
    }
    processStatus(this);
  }

  return function (oFormElement) {
    if (!oFormElement.action) { return; }
    new SubmitRequest(oFormElement);
  };

})();

&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with pure AJAX&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="text/plain" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      Your name: &lt;input type="text" name="user" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Your message:&lt;br /&gt;
      &lt;textarea name="message" cols="40" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt; &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt; &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt; &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt; &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;/body&gt;
&lt;/html&gt;</pre>

<p>Pour le tester, créez une page nommée <strong>register.php</strong> (qui est l'attribut <code>action</code> des formulaires d'exemple) et mettez y ce contenu <em>minimaliste</em>:</p>

<pre class="brush: php">&lt;?php
/* register.php */

header("Content-type: text/plain");

/*
NOTE: You should never use `print_r()` in production scripts, or
otherwise output client-submitted data without sanitizing it first.
Failing to sanitize can lead to cross-site scripting vulnerabilities.
*/

echo ":: data received via GET ::\n\n";
print_r($_GET);

echo "\n\n:: Data received via POST ::\n\n";
print_r($_POST);

echo "\n\n:: Data received as \"raw\" (text/plain encoding) ::\n\n";
if (isset($HTTP_RAW_POST_DATA)) { echo $HTTP_RAW_POST_DATA; }

echo "\n\n:: Files received ::\n\n";
print_r($_FILES);

</pre>

<p>La syntaxe de ce script est la suivante:</p>

<pre class="syntaxbox">AJAXSubmit(myForm);</pre>

<div class="note">
  <p><strong>Note :</strong> Ce framework utilise l'API <a href="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> pour transmettre les fichiers uploadés. C'est une API récente qui n'est pas implémentée dans IE9 ou inférieur. Pour cette raison, l'upload via AJAX uniquement <strong>est une technique expérimentale</strong>. Si vous n'avez pas besoin de transférer des fichiers binaires, ce framework fonctionne bien dans la majorité des navigateurs.</p>
</div>

<div class="note">
  <p><strong>Note :</strong> La meilleure façon d'envoyer du contenu binaire est de passer par <a href="/en-US/docs/JavaScript/Typed_arrays/ArrayBuffer">ArrayBuffers</a> ou <a href="/en-US/docs/DOM/Blob">Blobs</a> en conjonction avec la méthode <a href="/en-US/docs/DOM/XMLHttpRequest#send%28%29"><code>send()</code></a> et possiblement avec la méthode <a href="/en-US/docs/DOM/FileReader#readAsArrayBuffer()"><code>readAsArrayBuffer()</code></a> de l'API <a href="/en-US/docs/DOM/FileReader"><code>FileReader</code></a>. Mais dans la mesure où le but de ce script est de fonctionner avec des données <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">chaînifiable</a>, nous avons utilisé la méthode <a href="/en-US/docs/DOM/XMLHttpRequest#sendAsBinary%28%29"><code>sendAsBinary()</code></a> en conjonction avec la méthode <a href="/en-US/docs/DOM/FileReader#readAsBinaryString%28%29"><code>readAsBinaryString()</code></a> de l'API <a href="/en-US/docs/DOM/FileReader"><code>FileReader</code></a>. Du coup, le script ci-dessous n'a de sens que quand vous voulez transférer de petits fichiers. Si vous n'avez pas l'intention de transférer des données binaires, songez plutôt à utilisez l'API <a href="/en-US/docs/DOM/XMLHttpRequest/FormData"><code>FormData</code></a>.</p>
</div>

<div class="note">
  <p><strong>Note :</strong> La méthode non-strandard <code>sendAsBinary </code>est dépréciée à partir de Gecko 31 {{ geckoRelease(31) }} et sera prochainement supprimée. La méthode standard <code>send(Blob data)</code> peut être utilisée à la place.</p>
</div>

<h3 id="Utiliser_les_objets_FormData">Utiliser les objets FormData</h3>

<p>Le constructeur de <a href="/en-US/docs/DOM/XMLHttpRequest/FormData"><code>FormData</code></a> vous permet de compiler des paires de clé/valeur à envoyer via <code>XMLHttpRequest</code>. Il est principalement prévu pour être utilisé dans l'envoi de formulaires, mais il peut être utilisé indépendemment des formulaires dans le but de transmettre des données associées. Les données transmises sont de même format que la méthode <code>submit()</code> d'un formulaire utiliserait pour envoyer les données si l'encodage du formulaire était configuré à "multipart/form-data". Les objets FormData peuvent être utilisés de nombreuses manières avec XMLHttpRequest. Pour des exemples et des explications sur la manière d'utiliser FormData avec une XMLHttpRequest, jettez un oeil sur la page <a href="/en-US/docs/DOM/XMLHttpRequest/FormData/Using_FormData_Objects">Utiliser les Objets FormData</a>. Pour des raisons didactiques seulement, nous postons ici <strong>une</strong> <strong>traduction</strong> <strong><a href="#A_little_vanilla_framework">du précédent exemple</a> transformé pour utiliser l'API <code>FormData</code></strong>. Notez la brièveté du code :</p>

<pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;title&gt;Sending forms with FormData &amp;ndash; MDN&lt;/title&gt;
&lt;script type="text/javascript"&gt;
"use strict";

function ajaxSuccess () {
  alert(this.responseText);
}

function AJAXSubmit (oFormElement) {
  if (!oFormElement.action) { return; }
  var oReq = new XMLHttpRequest();
  oReq.onload = ajaxSuccess;
  if (oFormElement.method.toLowerCase() === "post") {
    oReq.open("post", oFormElement.action, true);
    oReq.send(new FormData(oFormElement));
  } else {
    var oField, sFieldType, nFile, sSearch = "";
    for (var nItem = 0; nItem &lt; oFormElement.elements.length; nItem++) {
      oField = oFormElement.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ? oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE") {
        for (nFile = 0; nFile &lt; oField.files.length; sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.files[nFile++].name));
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.value);
      }
    }
    oReq.open("get", oFormElement.action.replace(/(?:\?.*)?$/, sSearch.replace(/^&amp;/, "?")), true);
    oReq.send(null);
  }
}
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with FormData&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;p&gt;The text/plain encoding is not supported by the FormData API.&lt;/p&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt; &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt; &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt; &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt; &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;/body&gt;
&lt;/html&gt;</pre>

<div class="note">
  <p><strong>Note :</strong> Comme déjà dit, les objets<strong> {{domxref("FormData")}} ne sont pas des objets <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">chainifiables</a></strong>. Si vous voulez chainifier les données soumises, utilisez <a href="#A_little_vanilla_framework">l'exemple précédent en <em>pure</em>-AJAX</a>. Notez également que, bien que dans cet exemple il y a quelques champs <code>file</code> {{ HTMLElement("input") }}, <strong>quand vous soumettez un formulaire via l'API <code>FormData</code> vous n'avez pas besoin d'utiliser l'API <a href="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> également </strong>: les fichiers sont automatiquement chargés et transférés.</p>
</div>

<h2 id="Récupérer_la_date_de_modification">Récupérer la date de modification</h2>

<pre class="brush: js">function getHeaderTime () {
  alert(this.getResponseHeader("Last-Modified"));  /* Une chaine valide GMTString ou null */
}

var oReq = new XMLHttpRequest();
oReq.open("HEAD" /* utilisez HEAD seulement si vous ne voulez que les headers ! */, "mapage.html", true);
oReq.onload = getHeaderTime;
oReq.send();</pre>

<h3 id="Faire_quelque_chose_quand_la_date_de_dernière_modification_change">Faire quelque chose quand la date de dernière modification change</h3>

<p>Créons deux fonctions:</p>

<pre class="brush: js">function getHeaderTime () {

  var
    nLastVisit = parseFloat(window.localStorage.getItem('lm_' + this.filepath)),
    nLastModif = Date.parse(this.getResponseHeader("Last-Modified"));

  if (isNaN(nLastVisit) || nLastModif &gt; nLastVisit) {
    window.localStorage.setItem('lm_' + this.filepath, Date.now());
    isFinite(nLastVisit) &amp;&amp; this.callback(nLastModif, nLastVisit);
  }

}

function ifHasChanged(sURL, fCallback) {
  var oReq = new XMLHttpRequest();
  oReq.open("HEAD" /* utilisons HEAD - nous ne voulons que les Headers ! */, sURL, true);
  oReq.callback = fCallback;
  oReq.filepath = sURL;
  oReq.onload = getHeaderTime;
  oReq.send();
}</pre>

<p>Test:</p>

<pre class="brush: js">/* Testons le fichier "mapage.html"... */

ifHasChanged("mapage.html", function (nModif, nVisit) {
  alert("La page '" + this.filepath + "' a été changée le " + (new Date(nModif)).toLocaleString() + "!");
});</pre>

<p>Si vous voulez savoir <strong>si la <em>page courante</em> a changée</strong>, lisez l'article à propos de la propriété <a href="/en-US/docs/Web/API/document.lastModified"><code >document.lastModified</code></a>.</p>

<h2 id="Cross-site_XMLHttpRequest">Cross-site XMLHttpRequest</h2>

<p>Les navigateurs récents supportent les requêtes cross-site en implémentant le Standard <a href="/en-US/docs/HTTP_access_control">Access Control for Cross-Site Requests</a> (Web Application Working Group).  Tant que le serveur est configuré pour autoriser les requêtes depuis l'origine de votre web application, <code>XMLHttpRequest</code> fonctionnera. Sinon, une exception <code>INVALID_ACCESS_ERR</code> sera lancée.</p>

<h2 id="Contourner_le_cache">Contourner le cache</h2>

<p>Une approche cross-browser pour contourner le cache est d'ajouter le timestamp à l'URL, en étant sûr d'inclure un "?" ou un "&amp;" selon les cas. Par exemple :</p>

<pre>http://foo.com/bar.html -&gt; http://foo.com/bar.html?12345
http://foo.com/bar.html?foobar=baz -&gt; http://foo.com/bar.html?foobar=baz&amp;12345
</pre>

<p>Comme le cache local est indexé selon les URL, celà permet à toutes les requêtes d'être uniques, et du coup de contourner le cache.</p>

<p>Vous pouvez automatiquement ajuster les URL en utilisant le code suivant :</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.open("GET", url + ((/\?/).test(url) ? "&amp;" : "?") + (new Date()).getTime(), true);
oReq.send(null);</pre>

<h2 id="Sécurité">Sécurité</h2>

<p>{{fx_minversion_note(3, "Les versions de Firefox avant Firefox 3 autorisaient à mettre les préférences <code>capability.policy.&lt;policyname&gt;.XMLHttpRequest.open&lt;/policyname&gt;</code> à <code>allAccess</code> pour donner l'accès cross-sites à des sites spécifiques. Cela n'est plus possible.")}}</p>

<p>{{fx_minversion_note(5, "Les versions de Firefox avant Firefox 5 pouvaient utiliser <code>netscape.security.PrivilegeManager.enablePrivilege(\"UniversalBrowserRead\");</code> pour demander un accès cross-site. Ce n'est plus supporté, me^me si cela ne produit aucun avertissement et que la demande de permission est toujours présente.")}}</p>

<p>La manière recommandée d'activer les requêtes cross-sites est d'utiliser le header HTTP <code>Access-Control-Allow-Origin </code> dans la réponse du XMLHttpRequest.</p>

<h3 id="XMLHttpRequests_stoppées">XMLHttpRequests stoppées</h3>

<p>Si vous vous retrouvez avec une XMLHttpRequest ayant <code >status=0</code> et <code >statusText=null</code>, cela signifie que la requête n'a pas été autorisée à être effectuée. Elle a été <code ><a href="http://www.w3.org/TR/XMLHttpRequest/#dom-xmlhttprequest-unsent">UNSENT</a></code>. Une cause probable est lorsque <a href="http://www.w3.org/TR/XMLHttpRequest/#xmlhttprequest-origin">l'origine <code >XMLHttpRequest</code> </a> (lors de la création de l'objet XMLHttpRequest) a changé quand l'objet XMLHttpRequest est déjà open(). Ce cas peut se produire par exemple lorsque l'on a une XMLHttpRequest qui est lancée sur un évènement onunload d'une fenêtre: l'objet XMLHttpRequest est en fait créé lorsque la fenêtre sur le point de se fermer est toujours là, et la demande est envoyée (c'est à dire open()) lorsque cette fenêtre a perdu son focus et une autre fenêtre a potentiellement pris le focus. La manière d'éviter ce problème est de fixer un écouteur sur l'évènement "activate" de la nouvelle fenêtre qui se lance quand l'ancienne fenêtre a son événement "unload" lancé.</p>

<h2 id="Utiliser_XMLHttpRequest_depuis_un_module_JavaScript_un_composant_XPCOM">Utiliser XMLHttpRequest depuis un module JavaScript / un composant XPCOM</h2>

<p>Instancier une <code>XMLHttpRequest</code> depuis un <a href="/en-US/docs/JavaScript_code_modules/Using">module JavaScript</a> ou un composant XPCOM fonctionne un peu différemment; on ne peut pas l'instancier via le constructeur <code>XMLHttpRequest()</code>. Le constructeur n'est pas défini dans le composant et le code retourne une erreur. Le meilleur moyen de fixer le problème est d'utiliser le constructeur du composant XPCOM.</p>

<pre class="brush: js">const XMLHttpRequest = Components.Constructor("@mozilla.org/xmlextras/xmlhttprequest;1", "nsIXMLHttpRequest");
</pre>

<p>Malheureusement dans les versions de Gecko avant Gecko 16 il y a un bug : les requêtes créées ainsi peuvent être annulées sans raison. Si votre code doit marcher sur Gecko 15 ou moins, vous pouvez utiliser le constructeur XMLHttpRequest depuis le DOM caché de la fenêtre comme ceci :</p>

<pre class="brush:js">const { XMLHttpRequest } = Components.classes["@mozilla.org/appshell/appShellService;1"]
                                     .getService(Components.interfaces.nsIAppShellService)
                                     .hiddenDOMWindow;
var oReq = new XMLHttpRequest();</pre>

<h2 id="Voir_aussi">Voir aussi</h2>

<ol>
 <li><a href="/en-US/docs/AJAX/Getting_Started">MDN AJAX introduction</a></li>
 <li><a href="/en-US/docs/HTTP_access_control">HTTP access control</a></li>
 <li><a href="/en-US/docs/How_to_check_the_security_state_of_an_XMLHTTPRequest_over_SSL">How to check the security state of an XMLHTTPRequest over SSL</a></li>
 <li><a href="http://www.peej.co.uk/articles/rich-user-experience.html">XMLHttpRequest - REST and the Rich User Experience</a></li>
 <li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/xmlsdk/html/xmobjxmlhttprequest.asp">Microsoft documentation</a></li>
 <li><a href="http://developer.apple.com/internet/webcontent/xmlhttpreq.html">Apple developers' reference</a></li>
 <li><a href="http://jibbering.com/2002/4/httprequest.html">"Using the XMLHttpRequest Object" (jibbering.com)</a></li>
 <li><a href="http://www.w3.org/TR/XMLHttpRequest/">The XMLHttpRequest Object: W3C Specification</a></li>
 <li><a href="http://dev.w3.org/2006/webapi/progress/Progress.html">Web Progress Events specification</a></li>
 <li><a href="/fr/docs/Web/API/XMLHttpRequest">XMLHttpRequest (Référence Web API)</a></li>
</ol>