---
id: 1470
title: Crea scripts para las aplicaciones de Google con Apps Script
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=1470
permalink: /crea-scripts-para-las-aplicaciones-de-google-con-apps-script/
categories:
  - internet
tags:
  - Gmail Inbox Statistics Report
  - Informe estadístico mensual de GMail
---
Hace bastante tiempo que Google lanzó Apps Scripts, pero hasta ahora no lo había probado. Hoy esbozaré en qué consiste esta característica que google pone a nuestra disposición.

### Qué es Google Apps Script

Es un lenguaje de programación JavaScript en la nube que nos permite extender la funcionalidad de las aplicacoines de Google, así como crear las nuestras propias. Se desarrolla en un [editor desde el navegador web][1] y el código se guarda y ejecuta en los servidores de Google.

Varios ejemplos de uso son:  
  
<!--more-->

  * Construir funciones personalizadas para las hojas de Cálculo de Google.
  * Extender ciertas aplicaciones de Google creando menús personalizados y enlazarlos a scripts.
  * Crear y publicar aplicaciones web.
  * Programar tareas como la creación y distribución de informes.

Todo lo necesario para comenzar a escribir código es una cuenta de google, un navegador y dirigirse a <a href="http://script.google.com" target="_blank">http://script.google.com</a>

### Un ejemplo práctico. Informe estadístico mensual de GMail

Uno de los scripts que estoy usando actualmente recopila información de mi cuenta de gmail, para mandarme un correo al final de més con estadísticas y gráficos sobre quién me manda más correos electónicos, a quién respondo más etc. El informe que elabora este script es así:

<img src="http://elbauldelprogramador.com/content/uploads/2013/04/gmailStats.png" alt="gmailStats" width="495" height="244" class="aligncenter size-large wp-image-1514" />

<img src="http://elbauldelprogramador.com/content/uploads/2013/04/chart1.png" alt="chart1" width="650" height="400" class="aligncenter size-full wp-image-1512" />

<img src="http://elbauldelprogramador.com/content/uploads/2013/04/chart2.png" alt="chart2" width="650" height="400" class="aligncenter size-large wp-image-1513" />

Los pasos para configurar y dejar funcionando el script se pueden encontrar en este tutorial de Google »  
<a href="https://developers.google.com/apps-script/articles/gmail-stats" target="_blank">Tutorial: Creating Gmail Inbox Statistics Report</a>. 

Sin embargo, encontré un pequeño error que no dejará que se ejecute el script. En la línea 244 se declara una columna para la hoja de cálculo de tipo `STRING`, pero cuando se ejecuta el script dará error. La solución que encontré fue declarar el tipo de columna como numérico:

{% highlight javascript %}>var dataTable = Charts.newDataTable();
dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Date'); // Era STRING
dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Received');
dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Sent');
{% endhighlight %}

Para mayor comodidad, proporciono el script completo para que solo tengáis que copiar y pegar:

{% highlight javascript %}>/**
 * Check the date and the status of the report
 *
 */

function activityReport() {
  var status = UserProperties.getProperty("status");
  if (status != null) { //TODO. Es !=
    status = Utilities.jsonParse(status);
    // If new month, send new report
    if (status.previousMonth != new Date(
      new Date().getFullYear(),
      new Date().getMonth() - 1,
      1).getMonth()) {
      init_();
      fetchEmails_();
      status.previousMonth =
        new Date(new Date().getFullYear(), new Date().getMonth() - 1, 1).getMonth();
      status.reportSentforPreviousMonth = "no";
      UserProperties.setProperty("status", Utilities.jsonStringify(status));
    }
    // If report not sent, continue to work on the report
    else if (status.reportSentforPreviousMonth == "no") {
      fetchEmails_();
    }
  }
  // If the script is triggered for the first time, init and work on the report
  else {
    init_();
    fetchEmails_();
  }
}

function fetchEmails_() {
  var variables = Utilities.jsonParse(UserProperties.getProperty("variables"));
  var conversations =
    GmailApp.search(
      "in:anywhere after:" + variables.year + "/" + (variables.previousMonth) +
        "/31 before:" + new Date().getYear() + "/" +
        (variables.previousMonth + 1) +
        "/31 -label:sms -label:chats -label:spam",
      variables.range,
      100);
  variables.nbrOfConversations += conversations.length;
  for (i in conversations) {
    var youReplied = false;
    var youStartedTheConversation = false;
    var messages = conversations[i].getMessages();
    for (j in messages) {
      var date = messages[j].getDate();
      if (date.getMonth() == variables.previousMonth) {
        // ////////////////////////////////
        // Fetch sender of each emails
        // ////////////////////////////////
        var from = messages[j].getFrom();
        if (from.match(/&lt;/) != null) {
          from = from.match(/&lt;([^>]*)/)[1];
        }
        var time = Utilities.formatDate(date, variables.userTimeZone, "H");
        var day = Utilities.formatDate(date, variables.userTimeZone, "d");
        if (from == variables.user) {
          if (j == 0) {
            youStartedTheConversation = true;
          }
          if (j > 0 &#038;&#038; !youStartedTheConversation) {
            youReplied = true;
          }
          variables.nbrOfEmailsSent++;
          variables.timeOfEmailsSent[time]++;
          variables.dayOfEmailsSent[day]++;
          var to = messages[j].getTo();
          to = to.split(/,/);
          for (l = 0; l &lt; to.length; l = l + 2) {
            if (to[l].match(/&lt;/) != null) {
              to[l] = to[l].match(/&lt;([^>]*)/)[1];
            }
            for (k in variables.people) {
              if (to[l] == variables.people[k][0]) {
                variables.people[k][2]++;
                found = true;
              }
            }
            if (!found &#038;&#038; to[l].length &lt; 50) {
              variables.people.push([
                  to[l], 0, 1
              ]);
            }
          }
        } else {
          var found = false;
          for (k in variables.people) {
            if (from == variables.people[k][0]) {
              variables.people[k][1]++;
              found = true;
            }
          }
          if (!found &#038;&#038; from.length &lt; 50) {
            variables.people.push([
                from, 1, 0
            ]);
          }
          if (messages[j].getTo().search(variables.user) != -1) {
            variables.sentDirectlyToYou++;
          }
          variables.nbrOfEmailsReceived++;
          variables.timeOfEmailsReceived[time]++;
          variables.dayOfEmailsReceived[day]++;
        }
        if (messages[j].isInTrash()) {
          variables.nbrOfEmailsTrashed++;
        }
      }
    }
    if (youStartedTheConversation) {
      variables.nbrOfConversationsStartedByYou++;
    }
    if (youReplied) {
      variables.nbrOfConversationsYouveRepliedTo++;
    }
    if (conversations[i].hasStarredMessages()) {
      variables.nbrOfConversationsStarred++;
    }
    if (conversations[i].isImportant()) {
      variables.nbrOfConversationsMarkedAsImportant++;
    }
  }
  variables.range += 100;
  var error = true;
  while (error) {
    try {
      UserProperties.setProperty(
        "variables",
        Utilities.jsonStringify(variables));
      error = false;
    } catch (err) {
      variables.people.sort(sortArrayOfPeopleTo_);
      variables.people.pop();
      Utilities.sleep(2000);
    }
  }
  if (conversations.length &lt;= 100) {
    sendReport_(variables);
  }
}

function sendReport_(variables) {
  variables.people.sort(sortArrayOfPeopleFrom_);
  var report =
    "&lt;h2 style=\"color:#cccccc; font-family:verdana,geneva,sans-serif;\">Gmail Stats - " +
      Utilities.formatDate(
        new Date(variables.previous),
        variables.userTimeZone,
        "MMMM") +
      "&lt;/h2>

<p>
  " +
        variables.nbrOfConversations +
        " conversations - " +
        variables.nbrOfEmailsSent +
        " emails sent - " +
        variables.nbrOfEmailsReceived +
        " emails received - " +
        variables.nbrOfEmailsTrashed +
        " emails trashed<br />" +
        variables.nbrOfConversationsMarkedAsImportant +
        " conversations marked as important - " +
        variables.nbrOfConversationsStarred +
        " conversations starred.
</p>

<p>
  " +
        Math.round(variables.sentDirectlyToYou * 10000 /
          variables.nbrOfEmailsReceived) /
        100 +
        "% of those emails were sent directly to you. And you replied to " +
        Math.round(variables.nbrOfConversationsYouveRepliedTo *
          10000 /
          (variables.nbrOfConversations - variables.nbrOfConversationsStartedByYou)) /
        100 +
        "% of them.
</p>" +
      "&lt;table style=\"border-collapse: collapse;\">

<tr>
  &lt;td style=\"border: 0px solid white;\">" +
        "
  
  <h3>
    Top 5 senders:
  </h3>
  
  <ul>
    ";
      var r = 0;
      var s = 0;
      while (s &lt; 5) {
        if (variables.people[r][0].search(/notification|noreply|update/) == -1) {
          report +=
            "&lt;li title=\"" + variables.people[r][1] + " emails\">" +
              variables.people[r][0] + "&lt;/li>";
          s++;
        }
        r++;
      }
      variables.people.sort(sortArrayOfPeopleTo_);
      report +=
        "
  </ul>&lt;/td>&lt;td style=\"border: 0px solid white;\">
  
  <h3>
    Top 5 recipients:
  </h3>
  
  <ul>
    ";
      for (i = 0; i &lt; 5; i++) {
        report +=
          "&lt;li title=\"" + variables.people[i][2] + " emails\">" +
            variables.people[i][0] + "&lt;/li>";
      }
      report += "
  </ul>&lt;/td>
</tr>&lt;/table>

<br />&lt;img src=\'";

  var dataTable = Charts.newDataTable();
  dataTable.addColumn(Charts.ColumnType['STRING'], 'Time');
  dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Received');
  dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Sent');

  var time = '';
  for ( var i = 0; i &lt; variables.timeOfEmailsReceived.length; i++) { // create
                                                                      // the
                                                                      // rows
    switch (i) {
      case 8:
        time = '8am';
        break;
      case 10:
        time = '10am';
        break;
      case 12:
        time = '12pm';
        break;
      case 14:
        time = '2pm';
        break;
      case 18:
        time = '6pm';
        break;
      case 20:
        time = '8pm';
        break;
      default:
        time = '';
        break;
    }
    dataTable.addRow([
        time, variables.timeOfEmailsReceived[i], variables.timeOfEmailsSent[i]
    ]);
  }
  dataTable.build();
  var chartAverageFlow =
    Charts.newAreaChart().setDataTable(dataTable).setTitle("Average flow").setDimensions(
      650,
      400).build();

  report += "cid:Averageflow\'/>" + "&lt;img src=\'";

  var dataTable = Charts.newDataTable();
  dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Date'); // Era STRING
  dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Received');
  dataTable.addColumn(Charts.ColumnType['NUMBER'], 'Sent');

  for ( var i = 0; i &lt; variables.dayOfEmailsReceived.length; i++) { // create
                                                                    // the rows
    dataTable.addRow([
      i + 1, variables.dayOfEmailsReceived[i], variables.dayOfEmailsSent[i]
    ]);
  }
  dataTable.build();
  var chartDate =
    Charts.newAreaChart().setDataTable(dataTable).setTitle("Date").setDimensions(
      650,
      400).build();

  report += "cid:Date\'/>";

  var inlineImages = {};
  inlineImages['Averageflow'] = chartAverageFlow;
  inlineImages['Date'] = chartDate;

  MailApp.sendEmail(variables.user, "Gmail Stats", report, {
      htmlBody : report,
      inlineImages : inlineImages
  });
  var status = Utilities.jsonParse(UserProperties.getProperty("status"));
  status.reportSentforPreviousMonth = "yes";
  UserProperties.setProperty("status", Utilities.jsonStringify(status));
}

function sortArrayOfPeopleFrom_(a, b) {
  return ((a[1] > b[1]) ? -1 : ((a[1] &lt; b[1]) ? 1 : 0));
}

function sortArrayOfPeopleTo_(a, b) {
  return ((a[2] > b[2]) ? -1 : ((a[2] &lt; b[2]) ? 1 : 0));
}

function daysInMonth_(month, year) {
  return 32 - new Date(year, month, 32).getDate();
}


/**
 * Triggeret by activityReport at the begining of each month to clear the data stored
 */
function init_() {
  var dayOfEmailsSent = [];
  var dayOfEmailsReceived = [];
  var timeOfEmailsSent = [];
  var timeOfEmailsReceived = [];
  // Find previous month...
  var previous =
    new Date(new Date().getFullYear(), new Date().getMonth() - 1, 1);
  var previousMonth = previous.getMonth();
  var year = previous.getYear();
  var lastDay = daysInMonth_(previousMonth, year);
  for (i = 0; i &lt; lastDay + 1; i++) {
    dayOfEmailsSent[i] = 0;
    dayOfEmailsReceived[i] = 0;
  }
  for (i = 0; i &lt; 24; i++) {
    timeOfEmailsSent[i] = 0;
    timeOfEmailsReceived[i] = 0;
  }
  var userTimeZone = CalendarApp.getDefaultCalendar().getTimeZone();
  var user = Session.getActiveUser().getEmail();
  var variables = {
      range : 0,
      nbrOfConversations : 0,
      nbrOfConversationsYouveRepliedTo : 0,
      nbrOfConversationsStartedByYou : 0,
      nbrOfConversationsStarred : 0,
      nbrOfConversationsMarkedAsImportant : 0,
      nbrOfEmailsReceived : 0,
      nbrOfEmailsSent : 0,
      nbrOfEmailsTrashed : 0,
      sentDirectlyToYou : 0,
      people : [],
      dayOfEmailsSent : dayOfEmailsSent,
      dayOfEmailsReceived : dayOfEmailsReceived,
      timeOfEmailsSent : timeOfEmailsSent,
      timeOfEmailsReceived : timeOfEmailsReceived,
      previous : previous,
      previousMonth : previousMonth,
      year : year,
      lastDay : lastDay,
      userTimeZone : userTimeZone,
      user : user
  };
  UserProperties.setProperty("variables", Utilities.jsonStringify(variables));
  var status = {
      previousMonth : previousMonth,
      reportSentforPreviousMonth : "no"
  };
  UserProperties.setProperty("status", Utilities.jsonStringify(status));
}
{% endhighlight %}

#### Referencias

*Google Apps Script* **|** <a href="http://www.google.com/script/start/" target="_blank">Visitar sitio</a> 

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Crea scripts para las aplicaciones de Google con Apps Script+http://elbauldelprogramador.com/crea-scripts-para-las-aplicaciones-de-google-con-apps-script/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/crea-scripts-para-las-aplicaciones-de-google-con-apps-script/&t=Crea scripts para las aplicaciones de Google con Apps Script+http://elbauldelprogramador.com/crea-scripts-para-las-aplicaciones-de-google-con-apps-script/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Crea scripts para las aplicaciones de Google con Apps Script+http://elbauldelprogramador.com/crea-scripts-para-las-aplicaciones-de-google-con-apps-script/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
      </li>
    </ul>
  </div>
</div>

<span id="socialbottom" class="highlight style-2">

<p>
  <strong>¿Eres curioso? » <a onclick="javascript:_gaq.push(['_trackEvent','random','click-random']);" href="/index.php?random=1">sigue este enlace</a></strong>
</p>

<h6>
  Únete a la comunidad
</h6>

<div class="iconsc hastip" title="2240 seguidores">
  <a href="http://twitter.com/elbaulp" target="_blank"><i class="icon-twitter"></i></a>
</div>

<div class="iconsc hastip" title="2452 fans">
  <a href="http://facebook.com/elbauldelprogramador" target="_blank"><i class="icon-facebook"></i></a>
</div>

<div class="iconsc hastip" title="0 +1s">
  <a href="http://plus.google.com/+Elbauldelprogramador" target="_blank"><i class="icon-google-plus"></i></a>
</div>

<div class="iconsc hastip" title="Repositorios">
  <a href="http://github.com/algui91" target="_blank"><i class="icon-github"></i></a>
</div>

<div class="iconsc hastip" title="Feed RSS">
  <a href="http://elbauldelprogramador.com/feed" target="_blank"><i class="icon-rss"></i></a>
</div></span>

 [1]: http://elbauldelprogramador.com/articulos/futuro-ides-de-escritorio/ "5 Razones por las cuales en 5 años los IDEs de escritorio estarán muertos"