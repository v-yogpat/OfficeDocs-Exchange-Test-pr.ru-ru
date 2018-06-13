﻿---
title: Устранение неполадок в наборе для контроля работоспособности POP
TOCTitle: Устранение неполадок в наборе для контроля работоспособности POP
ms:assetid: 6114e9fe-d037-4cb9-a643-933eb5fabc45
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/ms.exch.scom.pop(v=EXCHG.150)
ms:contentKeyID: 53275660
ms.date: 11/14/2015
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Устранение неполадок в наборе для контроля работоспособности POP

 

_**Применимо к:**Exchange Server 2013_

_**Последнее изменение раздела:**2015-03-09_

Набор для контроля работоспособности POP отслеживает общую исправность и доступность службы POP3 Microsoft Exchange и подключений клиентов по протоколу POP3. Набор для контроля работоспособности POP тесно связан со следующими наборами:

  - [Устранение неполадок, связанных с настройками работоспособности POP.Protocol](troubleshooting-pop-protocol-health-set.md)

  - [Устранение неполадок, связанных с настройками работоспособности POP.Proxy](troubleshooting-pop-proxy-health-set.md)

Если вы получаете оповещение, что служба POP неисправна, значит какая-то проблема мешает пользователям получить доступ к своим почтовым ящикам по протоколу POP3 в среде Exchange.

## Пояснение

Служба POP отслеживается следующими зондами и мониторами.


<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 25%" />
<col style="width: 25%" />
<col style="width: 25%" />
</colgroup>
<thead>
<tr class="header">
<th>Зонд</th>
<th>Настройки работоспособности</th>
<th>Зависимости</th>
<th>Связанные мониторы</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>PopCTPProbe</p></td>
<td><p>POP</p></td>
<td><p>Active Directory</p>
<p>Проверка подлинности</p>
<p>Проверка подлинности сервера почтовых ящиков</p>
<p>Банк данных</p>
<p>Высокая доступность</p>
<p>Сеть</p></td>
<td><p>PopCTPMonitor (набор для контроля работоспособности POP)</p></td>
</tr>
<tr class="even">
<td><p>PopProxyTestProbe</p></td>
<td><p>POP.Proxy</p></td>
<td><p>Нет</p></td>
<td><p>PopProxyTestMonitor (набор для контроля работоспособности POP.Proxy)</p></td>
</tr>
<tr class="odd">
<td><p>PopDeepTestProbe</p></td>
<td><p>POP.Protocol</p></td>
<td><p>Active Directory</p>
<p>Проверка подлинности</p>
<p>Банк данных</p>
<p>Высокая доступность</p></td>
<td><p>PopDeepTestMonitor (набор для контроля работоспособности POP.Protocol)</p></td>
</tr>
<tr class="even">
<td><p>PopSelfTestProbe</p></td>
<td><p>POP.Protocol</p></td>
<td><p>Нет</p></td>
<td><p>PopSelfTestMonitor (набор для контроля работоспособности POP.Protocol)</p>
<p>AverageCommandProcessingTimeGt60sMonitor (набор для контроля работоспособности POP)</p></td>
</tr>
</tbody>
</table>


## Действия пользователя

Служба может восстановить работу после отображения оповещения. Поэтому если вы получите оповещение о неработоспособном состоянии настроек работоспособности, сначала убедитесь в наличии данной проблемы. Если проблема не устранена, выполните соответствующие действия по восстановлению, указанные в приведенных ниже разделах.

## Проверка наличия проблемы

1.  Определите имена настроек работоспособности и сервера, указанные в оповещении.

2.  В сообщении приводятся подробные сведения о точной причине возникновения оповещения. В большинстве случаев в сообщении приводится достаточно сведений по устранению неполадок для определения основной причины проблемы. Если в сообщении приводятся непонятные сведения:
    
    1.  Откройте Командная консоль Exchange, а затем выполните следующую команду, чтобы извлечь подробные сведения о настройках работоспособности, с которыми связано оповещение.
        
            Get-ServerHealth <server name> | ?{$_.HealthSetName -eq "<health set name>"}
        
        Например, чтобы получить сведения о наборе для контроля работоспособности POP для сервера server1.contoso.com, выполните следующую команду:
        
            Get-ServerHealth server1.contoso.com | ?{$_.HealthSetName -eq "POP"}
    
    2.  Просмотрите выходные данные команды, чтобы определить монитор, сообщивший об ошибке. Параметр **AlertValue** монитора, вызвавшего оповещение, будет иметь значение `Unhealthy`.
    
    3.  Еще раз запустите зонд для средства мониторинга, которое находится в неисправном состоянии. Обратитесь к таблице в разделе Пояснение, чтобы найти связанный зонд. Для этого выполните следующую команду.
        
            Invoke-MonitoringProbe <health set name>\<probe name> -Server <server name> | Format-List
        
        Например, предположим, что неисправен монитор **PopCTPMonitor**. С ним будет связан зонд **PopCTPProbe**. Чтобы запустить этот зонд на сервере server1.contoso.com, выполните следующую команду.
        
            Invoke-MonitoringProbe POP\PopCTPProbe -Server server1.contoso.com | Format-List
    
    4.  В выходных данных команды просмотрите значение параметра **Результат** зонда. Если этот параметр имеет значение **Succeeded**, ошибка была временной и в настоящее время устранена. В противном случае обратитесь к действиям по восстановлению, приведенным в следующих разделах.

## Действия для восстановления мониторов PopTestDeepMonitor и PopSelfTestMonitor

Это оповещение обычно возникает на серверах почтовых ящиков.

1.  Перезапустите внутреннюю службу POP3. Подробнее см. в разделе [Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\)).

2.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

3.  Если зонд все равно вызывает ошибку, выполните отработку отказа баз данных, размещенных на сервере почтовых ящиков, с помощью следующей команды:
    
        Set-MailboxServer -Identity <ServerName> -DatabaseCopyActivationDisabledAndMoveNow $true

4.  После удаления всех баз данных с сервера почтовых ящиков убедитесь, что они успешно перемещены. Для этого выполните следующую команду.
    
        Get-MailboxDatabaseCopyStatus -Server <ServerName> | group status

5.  Убедитесь, что на сервере нет активных копий баз данных. Затем перезагрузите сервер.

6.  После успешной перезагрузки сервера перезапустите связанный зонд, как показано на шаге 2в в разделе Проверка наличия проблемы.

7.  Если с зондом все нормально, выполните отработку отказа баз данных с помощью следующей команды:
    
        Set-MailboxServer -Identity <ServerName> -DatabaseCopyActivationDisabledAndMoveNow $false

8.  Если зонд все еще не работает, вам понадобится помощь для устранения данной проблемы. Для решения этой проблемы обратитесь к специалисту службы технической поддержки Майкрософт. Сделать это можно в [Центре решений Exchange Server](http://go.microsoft.com/fwlink/p/?linkid=180809). В области навигации выберите элемент **Варианты поддержки и ресурсы** и выберите один из вариантов в разделе **Получите техническую поддержку**, чтобы обратиться к соответствующему специалисту. Так прямое обращение в службу технической поддержки Майкрософт в вашей организации может регламентироваться, сначала ознакомьтесь с инструкциями организации.

## Действия для восстановления монитора POPCTPMonitor

Это оповещение обычно возникает на серверах клиентского доступа (CAS).

1.  Перезапустите службу POP3 на серверах с ролью CAS. Подробнее см. в разделе [Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\)).

2.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

3.  Если проблема не устранена, выполните следующие команды в Windows PowerShell:
    
    1.  ``` 
        Set-PopSettings -server <CAS server name> -ProtocolLoggingEnabled $true
        ```
    
    2.  Перезапустите службу POP3 на серверах с ролью CAS.
    
    3.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.
    
    4.  Чтобы найти файл журнала, выполните следующую команду:
        
            Get-PopSettings -server <CAS server name>
    
    5.  Выполните следующую команду, чтобы определить, в каком почтовом ящике выполняется команда, сравнив временные метки с зондом:
        
            Get-ServerHealth <mailbox server name> | ?{ $_.HealthSetName -like "POP*"}
    
    6.  Если какие-либо из этих серверов неисправны, выполните действия, описанные в разделе Действия для восстановления мониторов PopTestDeepMonitor и PopSelfTestMonitor.

4.  Если сервер почтовых ящиков указан как неисправный, перезапустите сервер CAS.

5.  После перезагрузки сервера перезапустите связанный зонд, как показано на шаге 2в в разделе Проверка наличия проблемы.

6.  Отключите ведение журнала протокола, выполнив следующую команду:
    
        Set-PopSettings -server <CAS server name> -ProtocolLoggingEnabled $false

7.  Перезапустите службу POP3 на серверах с ролью CAS. Подробнее см. в разделе [Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\)).

8.  Если зонд все еще не работает, вам понадобится помощь для устранения данной проблемы. Для решения этой проблемы обратитесь к специалисту службы технической поддержки Майкрософт. Сделать это можно в [Центре решений Exchange Server](http://go.microsoft.com/fwlink/p/?linkid=180809). В области навигации выберите элемент **Варианты поддержки и ресурсы** и выберите один из вариантов в разделе **Получите техническую поддержку**, чтобы обратиться к соответствующему специалисту. Так прямое обращение в службу технической поддержки Майкрософт в вашей организации может регламентироваться, сначала ознакомьтесь с инструкциями организации.

## Действия для восстановления монитора PopProxyTestMonitor

1.  Перезапустите службу POP3 на серверах с ролью CAS. Подробнее см. в разделе [Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\)).

2.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

3.  Если проблема с зондом осталась, перезапустите сервер CAS.

4.  После перезагрузки сервера перезапустите связанный зонд, как показано на шаге 2в в разделе Проверка наличия проблемы.

5.  Если зонд все еще не работает, вам понадобится помощь для устранения данной проблемы. Для решения этой проблемы обратитесь к специалисту службы технической поддержки Майкрософт. Сделать это можно в [Центре решений Exchange Server](http://go.microsoft.com/fwlink/p/?linkid=180809). В области навигации выберите элемент **Варианты поддержки и ресурсы** и выберите один из вариантов в разделе **Получите техническую поддержку**, чтобы обратиться к соответствующему специалисту. Так прямое обращение в службу технической поддержки Майкрософт в вашей организации может регламентироваться, сначала ознакомьтесь с инструкциями организации.

## Действия для восстановления монитора AverageCommandProcessingTimeGt60sMonitor

Это оповещение обычно возникает на серверах клиентского доступа или серверах почтовых ящиков.

1.  Перезапустите службу POP3 на серверах клиентского доступа или серверах почтовых ящиков. Подробнее см. в разделе [Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\)).

2.  Подождите 10 минут, чтобы проверить, правильно ли работает монитор. Через 10 минут выполните следующую команду (в примере используется сервер server1.contoso.com).
    
        Get-ServerHealth server1.contoso.com | ?{$_.HealthSetName -like "POP*"}

3.  Если проблема осталась, перезапустите сервер. Если это сервер CAS, просто перезагрузите его. Если это сервер почтовых ящиков, необходимо выполнить отработку отказа базы данных и проверить результаты. Для этого выполните указанные ниже действия.
    
    1.  Выполните отработку отказа баз данных, размещенных на сервере, с помощью следующей команды:
        
            Set-MailboxServer -Identity <ServerName> -DatabaseCopyActivationDisabledAndMoveNow $true
        
        **Примечание**. В этом и всех последующих примерах кодов замените *server1.contoso.com* на действительное имя сервера.
    
    2.  Убедитесь, что все базы данных перемещены с сервера, сообщающего о проблеме. Для этого выполните следующую команду.
        
            Get-MailboxDatabaseCopyStatus -Server <ServerName> | group status
        
        Если в выходных данных команды не отображаются активные копии на сервере, перезапустите сервер.

4.  После перезапуска сервера подождите 10 минут, а затем снова запустите команду, указанную в шаге 2, чтобы проверить, правильно ли работает монитор.

5.  Если монитор исправен и это сервер почтовых ящиков, выполните отработку отказа баз данных на сервер с помощью следующей команды:
    
        Set-MailboxServer -Identity <ServerName> -DatabaseCopyActivationDisabledAndMoveNow $false

6.  Если зонд все еще не работает, вам понадобится помощь для устранения данной проблемы. Для решения этой проблемы обратитесь к специалисту службы технической поддержки Майкрософт. Сделать это можно в [Центре решений Exchange Server](http://go.microsoft.com/fwlink/p/?linkid=180809). В области навигации выберите элемент **Варианты поддержки и ресурсы** и выберите один из вариантов в разделе **Получите техническую поддержку**, чтобы обратиться к соответствующему специалисту. Так прямое обращение в службу технической поддержки Майкрософт в вашей организации может регламентироваться, сначала ознакомьтесь с инструкциями организации.

## Дополнительные сведения

[Запуск и остановка служб POP3](https://technet.microsoft.com/ru-ru/library/aa997475\(v=exchg.150\))

[Командлет Test-PopConnectivity](https://technet.microsoft.com/ru-ru/library/bb738143\(v=exchg.150\))
