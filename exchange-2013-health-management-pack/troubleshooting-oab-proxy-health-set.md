﻿---
title: Устранение неполадок с настройками работоспособности OAB.Proxy
TOCTitle: Устранение неполадок с настройками работоспособности OAB.Proxy
ms:assetid: b717fc00-a787-44d6-8ccb-0eb4b2ea9e73
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/ms.exch.scom.oab.proxy(v=EXCHG.150)
ms:contentKeyID: 53275670
ms.date: 11/14/2015
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# Устранение неполадок с настройками работоспособности OAB.Proxy

 

_**Применимо к:**Exchange Server 2013, Project Server 2013_

_**Последнее изменение раздела:**2015-03-09_

Настройки работоспособности OAB.Proxy отслеживают доступность инфраструктуры прокси-сервера автономной адресной книги (OAB) на сервере клиентского доступа (CAS).

Если вы получили оповещение о неисправности OAB.Proxy, значит существует проблема, которая может предотвращать ваш доступ к OAB.

## Пояснение

Слежение за службой OAB выполняется с помощью следующих зондов и средств мониторинга.


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
<td><p>OABProxyTestProbe</p></td>
<td><p>OAB.Proxy</p></td>
<td><p>Active Directory</p></td>
<td><p>OABProxyTestMonitor</p></td>
</tr>
</tbody>
</table>


Дополнительные сведения о зондах и мониторах см. в разделе [Работоспособность и производительность сервера](https://technet.microsoft.com/ru-ru/library/jj150551\(v=exchg.150\)).

## Распространенные проблемы

Проблема с этим зондом может произойти по любой из следующих причин.

  - Пул приложений, размещенный на отслеживаемом сервере клиентского доступа, работает неправильно.

  - Введены неправильные данные учетной записи наблюдения.

  - Контроллеры доменов не отвечают.

## Действия пользователя

Служба может восстановить работу после отображения оповещения. Поэтому если вы получите оповещение о неработоспособном состоянии настроек работоспособности, сначала убедитесь в наличии данной проблемы. Если проблема не устранена, выполните соответствующие действия по восстановлению, указанные в приведенных ниже разделах.

## Проверка наличия проблемы

1.  Определите имена настроек работоспособности и сервера, указанные в оповещении.

2.  В сообщении приводятся подробные сведения о точной причине возникновения оповещения. В большинстве случаев в сообщении приводится достаточно сведений по устранению неполадок для определения основной причины проблемы. Если в сообщении приводятся непонятные сведения:
    
    1.  Откройте Командная консоль Exchange, а затем выполните следующую команду, чтобы извлечь подробные сведения о настройках работоспособности, с которыми связано оповещение.
        
            Get-ServerHealth <server name> | ?{$_.HealthSetName -eq "<health set name>"}
        
        Например, чтобы получить сведения о настройках работоспособности OAB.Proxy для server1.contoso.com, выполните следующую команду:
        
            Get-ServerHealth server1.contoso.com | ?{$_.HealthSetName -eq "OAB.Proxy"}
    
    2.  Просмотрите выходные данные команды, чтобы определить монитор, сообщивший об ошибке. Параметр **AlertValue** монитора, вызвавшего оповещение, будет иметь значение `Unhealthy`.
    
    3.  Повторно запустите связанный зонд для монитора, находящегося в неработоспособном состоянии. Обратитесь к таблице в разделе Пояснение, чтобы найти связанный зонд. Для этого выполните следующую команду.
        
            Invoke-MonitoringProbe <health set name>\<probe name> -Server <server name> | Format-List
        
        Допустим, что неисправно средство мониторинга **OABProxyTestMonitor**. Связанный с этим средством мониторинга зонд — **OABProxyTestProbe**. Чтобы запустить этот зонд на сервере server1.contoso.com, выполните следующую команду.
        
            Invoke-MonitoringProbe |OAB.Proxy\OABProxyTestProbe -Server server1.contoso.com | Format-List
    
    4.  В выходных данных команды просмотрите значение параметра **Результат** зонда. Если этот параметр имеет значение **Succeeded**, ошибка была временной и в настоящее время устранена. В противном случае обратитесь к действиям по восстановлению, приведенным в следующих разделах.

## Действия по восстановлению OABProxyTestMonitor

Если вы получите оповещение, связанное с настройками работоспособности, сообщение электронной почты содержит приведенные ниже сведения.

  - Имя сервера клиентского доступа, отправившего оповещение.

  - Полная трассировка исключения, связанного с последней ошибкой, включая диагностические данные и конкретные сведения о заголовке HTTP.  
    
    **Примечание**. Сведения в полной трассировке исключения можно использовать для устранения проблемы.

  - Время и дата возникновения проблемы.

Для устранения данной проблемы выполните действия, указанные ниже.

1.  Просмотрите журналы протокола на сервере клиентского доступа. Журналы протокола расположены в папке *\<каталог установки Exchange Server\>*\\Logging\\HttpProxy*\\\<protocol\>* на сервере клиентского доступа.

2.  Создайте тестовую учетную запись пользователя, а затем войдите из нее на сервер клиентского доступа. Например, войдите в систему со следующего адреса: https:// *\<имя\_сервера\>*/owa.

3.  Запустите диспетчер IIS, а затем подключитесь к серверу, который сообщает о проблеме, чтобы определить, работает ли пул приложений **MSExchangeOABAppPool** на сервере клиентского доступа.

4.  Нажмите **Пулы приложений**, а затем перезапустите пул **MSExchangeOABAppPool**, выполнив следующую команду в командной консоли:
    
        %SystemRoot%\System32\inetsrv\Appcmd recycle MSExchangeOABAppPool

5.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

6.  Если проблема не устранена, перезапустите службы IIS с помощью служебной программы IISReset.

7.  Повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

8.  Если проблема не устранена, перезапустите сервер.

9.  После перезапуска сервера повторно запустите связанный зонд, как показано в шаге 2c раздела Проверка наличия проблемы.

10. Если зонд все еще не работает, вам понадобится помощь для устранения данной проблемы. Для решения этой проблемы обратитесь к специалисту службы технической поддержки Майкрософт. Сделать это можно в [Центре решений Exchange Server](http://go.microsoft.com/fwlink/p/?linkid=180809). В области навигации выберите элемент **Варианты поддержки и ресурсы** и выберите один из вариантов в разделе **Получите техническую поддержку**, чтобы обратиться к соответствующему специалисту. Так прямое обращение в службу технической поддержки Майкрософт в вашей организации может регламентироваться, сначала ознакомьтесь с инструкциями организации.

## Дополнительные сведения

[автономные адресные книги,](https://technet.microsoft.com/ru-ru/library/bb232155\(v=exchg.150\))

[Новые возможности в Exchange 2013](https://technet.microsoft.com/ru-ru/library/jj150540\(v=exchg.150\))

[Командлеты Exchange 2013](https://technet.microsoft.com/ru-ru/library/bb124413\(v=exchg.150\))

