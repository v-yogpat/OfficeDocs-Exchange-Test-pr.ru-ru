﻿---
title: 'Настройка Exchange для поддержки делегированных разрешений почтовых ящиков в гибридном развертывании: Exchange 2013 Help'
TOCTitle: Настройка Exchange для поддержки делегированных разрешений почтовых ящиков в гибридном развертывании
ms:assetid: a2a10cb3-4557-4ff5-8191-c653522f4512
ms:mtpsurl: https://technet.microsoft.com/ru-ru/library/Mt784505(v=EXCHG.150)
ms:contentKeyID: 74447331
ms.date: 05/23/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# Настройка Exchange для поддержки делегированных разрешений почтовых ящиков в гибридном развертывании

 

_**Последнее изменение раздела:**2018-01-30_

С помощью делегированных разрешений для почтовых ящиков пользователи могут частично управлять почтовыми ящиками других пользователей. Распространенный пример такого пользователя — помощник администратора, который должен управлять почтой и календарем руководителя. Гибридные развертывания, состоящие из локальной организации Exchange и Office 365, поддерживают делегированные разрешения **Полный доступ** и **Отправить от имени** для почтовых ящиков. Однако в зависимости от того, какая версия Exchange установлена в локальной организации, для использования делегированных разрешений почтовых ящиков может потребоваться дополнительная настройка. В приведенной ниже таблице перечислены версии Exchange, которые поддерживают делегированные разрешения для почтовых ящиков в гибридном развертывании и указано, требуется ли дополнительная настройка для той или иной версии.

  - **Exchange 2016**. Дополнительная настройка не требуется.

  - **Exchange 2013**. Необходимы поддерживаемый накопительный пакет обновления (CU) для Exchange 2013 и дополнительная настройка.

  - **Exchange 2010**. Необходимы поддерживаемый накопительный пакет обновления (RU) для Exchange 2010 и дополнительная настройка.

Дополнительные сведения о конкретных требованиях для поддержки делегированных разрешений почтовых ящиков в гибридном развертывании см. в статье [Разрешения при гибридных развертываниях Exchange](permissions-in-exchange-hybrid-deployments-exchange-2013-help.md).

В следующих разделах описывается настройка локальных развертываний Exchange 2013 и Exchange 2010 для поддержки делегированных разрешений почтовых ящиков. Прежде чем выполнять эти действия, убедитесь, что установлен последний накопительный пакет обновления (CU или RU) для Exchange 2013 или Exchange 2010 с пакетом обновления 3 (SP3). Дополнительные сведения см. в статье [Предварительные условия для гибридного развертывания](hybrid-deployment-prerequisites-exchange-2013-help.md).

## Exchange 2013

Действия, необходимые для включения поддержки делегированных разрешений почтовых ящиков, зависят от ряда факторов. Если вы переносили почтовые ящики в Office 365, воспользуйтесь приведенной ниже таблицей.


<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th>Установленная среда</th>
<th>Состояние синхронизации объектов, поддерживающих ACL, в организации</th>
<th>Необходимые действия</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Exchange 2013 с накопительным пакетом обновления 9 (CU9) или более ранней версии</p></td>
<td><p>Эта функция недоступна в Exchange 2013 с накопительным пакетом обновления 9 (CU9) и более ранних версий.</p></td>
<td><p>Вручную настройте поддержку ACL для каждого почтового ящика</p></td>
</tr>
<tr class="even">
<td><p>Exchange 2013 с накопительным пакетом обновления 10 (CU10) или более поздней версии</p></td>
<td><p>Отключена</p></td>
<td><ol>
<li><p>Включите синхронизацию объектов, поддерживающих ACL, на уровне организации</p></li>
<li><p>Вручную включите ACL для каждого почтового ящика, перенесенного в Office 365 до того, как синхронизация объектов, поддерживающих ACL, была включена на уровне организации.</p></li>
</ol>
<p>Для почтовых ящиков, перемещенных в Office 365 после того, как на уровне организации была включена синхронизация объектов, поддерживающих ACL, не потребуется дополнительная настройка.</p></td>
</tr>
<tr class="odd">
<td><p>Exchange 2013 с накопительным пакетом обновления 10 (CU10) или более поздней версии</p></td>
<td><p>Включена</p></td>
<td><p>Дополнительная настройка не требуется.</p></td>
</tr>
</tbody>
</table>


## Включение синхронизации объектов, поддерживающих ACL

Чтобы включить синхронизацию объектов, поддерживающих ACL, на уровне организации, выполните указанные ниже действия.

1.  Установите последнюю версию Azure Active Directory Connect (AAD Connect) на всех серверах AAD Connect. Это необходимо, чтобы сервер AAD Connect мог синхронизировать атрибуты, необходимые для поддержки гибридных разрешений. Вы можете скачать AAD Connect на странице [Microsoft Azure Active Directory Connect](http://go.microsoft.com/fwlink/p/?linkid=510956).

2.  Откройте командную консоль Exchange на сервере с последним или предыдущим накопительным пакетом обновления (CU) для Exchange 2013.

3.  Выполните следующую команду:
    
        Set-OrganizationConfig -ACLableSyncedObjectEnabled $True

После этого все почтовые ящики, перемещенные в Office 365, будут надлежащим образом настроены для поддержки делегированных разрешений почтовых ящиков. Если почтовые ящики были перемещены в Office 365 до выполнения этих действий, потребуется вручную включить ACL для этих почтовых ящиков, выполнив действия, описанные в разделе Включение ACL для удаленных почтовых ящиков.

<table>
<thead>
<tr class="header">
<th><img src="images/Dn151301.important(EXCHG.150).gif" title="Важно" alt="Важно" />Важно!</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Списки управления доступом не включен на удаленные почтовые ящики, созданные в Office 365. Если вы создаете удаленного почтового ящика в Office 365, необходимо выполните действия в списки управления доступом включить в разделе удаленные почтовые ящики, чтобы включить списки управления доступом на этот удаленный почтовый ящик. Чтобы избежать этой дополнительное действие, рекомендуется создать почтовый ящик на сервере Exchange в локальной и затем перемещения почтовых ящиков в Office 365.</td>
</tr>
</tbody>
</table>


## Включение ACL для удаленных почтовых ящиков

Чтобы включить ACL для почтовых ящиков, перенесенных в Office 365 до того, как синхронизация объектов, поддерживающих ACL, была включена на уровне организации, выполните указанные ниже действия.

1.  Откройте командную консоль Exchange на сервере с последним или предыдущим накопительным пакетом обновления (CU) для Exchange 2013.

2.  Чтобы включить ACL для одного почтового ящика, выполните следующую команду:
    
        Get-AdUser <Identity> | Set-AdObject -Replace @{msExchRecipientDisplayType=-1073741818}

3.  Чтобы включить ACL для всех почтовых ящиков, перемещенных в Office 365, выполните следующую команду:
    
        Get-RemoteMailbox | ForEach { Get-AdUser -Identity $_.Guid | Set-ADObject -Replace @{msExchRecipientDisplayType=-1073741818}}

4.  Чтобы убедиться, что почтовые ящики успешно обновлены, выполните следующую команду:
    
        Get-RemoteMailbox | ForEach { Get-AdUser -Identity $_.Guid -Properties msExchRecipientDisplayType | Format-Table -AutoSize DistinguishedName, msExchRecipientDisplayType}

## Exchange 2010

Серверы Exchange Server 2010 с пакетом обновления (SP3) поддерживают настройку ACL в удаленных почтовых ящиках, но эту конфигурацию необходимо вручную задать для каждого почтового ящика. В отличие от новых версий Exchange, в Exchange 2010 нет возможности включить эту функцию на уровне организации. Указанные ниже действия необходимо выполнить для каждого почтового ящика, ранее перемещенного в Office 365, и для всех почтовых ящиков, которые будут перемещены с сервера Exchange Server 2010 с пакетом обновления 3 (SP3) в Office 365.

## Включение ACL для удаленных почтовых ящиков

Чтобы включить ACL для почтовых ящиков, перемещенных в Office 365, выполните указанные ниже действия.

1.  Откройте командную консоль Exchange на сервере с последним или предыдущим накопительным пакетом обновления (RU) для Exchange 2010 с пакетом обновления 3 (SP3).

2.  Чтобы включить ACL для одного почтового ящика, выполните следующую команду:
    
        Get-AdUser <Identity> | Set-AdObject -Replace @{msExchRecipientDisplayType=-1073741818}

3.  Чтобы включить ACL для всех почтовых ящиков, перемещенных в Office 365, выполните следующую команду:
    
        Get-RemoteMailbox | ForEach { Get-AdUser -Identity $_.Guid | Set-ADObject -Replace @{msExchRecipientDisplayType=-1073741818}}

4.  Чтобы убедиться, что почтовые ящики успешно обновлены, выполните следующую команду:
    
        Get-RemoteMailbox | ForEach { Get-AdUser -Identity $_.Guid -Properties msExchRecipientDisplayType | Format-Table -AutoSize DistinguishedName, msExchRecipientDisplayType}
