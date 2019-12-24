ТЗ на алготиртм.

# Обязательно:
## buffer
Возможность создавать группы буферы

## Инициатива поднимать цену за бота
Чем выше группа, тем должна быть выше цена за бота. 

# Желательно:
## short
Инициатива делать цепочку короче

## price-increase
Чем выше группа, тем цена за бота выше (или такая же)

# Подумать
## 0-payment
Возможность создавать группы с нулевой наградой. Или может быть эта награда будет равна награде нижестоящей группы?

Такая группа ничем не рискует. Нужна ли такая группа в принципе? Это может быть приложение. __Приложениям должно быть легко добавлять группы__, которым они доверяют. Пока на этой возможности планируется строить различные схемы оплаты. 

Или это группы, которые не планируют вступать куда-то дальше. Информация для себя. Ну, приложения, для простоты.

## botnet-reward
Возможность целой ветке взорваться. Супер-наказание. 

## invite-free
Невозможно включить члена в группу без его ведома и навариться на этом. При таком алгоритме нет необходимости подтверждать приглашения. 

Например. Появляется более дорогая группа сверху. Она определяет "надбавку" за бота. Добавляя более дешевую группу, она рискует. 
Ну, а более низкая группа рискует тем, что кто-то теперь решит взорваться. Нормально. Но никому из этих групп нет смысла создавать более дорогую группу сверху ради наживы, потому что они ничего не получат сверх.  

## spam-free


# Примеры
## Алгоритм 1. Максимальные выплаты сверху.
## +invite-free, +buffer, +0-payment, +short
## 

Вычисляем bot_reward в верхней группе:
    bot_reward = max_bot_reward * user_score
В этой группе выплачиваем current_bot_reward: 
    current_bot_reward = bot_reward - bot_reward всех нижестоящих. 

## Алгоритм 2. Максимальные выплаты снизу.
## +buffer, +0-payment, 
## -invite-free

    function attack(address[] calldata path, address payable bot, uint remainingReward) external onlyMember {
        // calculate maxBotReward and payout the sum starting from the bottom. Stop when the sum is payed.
        uint currentGroupReward = maxBotReward * membersScores[msg.sender];  // todo is this simplification correct?
        IUpalaGroup nextUpalaGroup = IUpalaGroup(path[path.length-1]);
        
        _rewardBot(bot, currentGroupReward);
        
        address[] memory newPath = path;
        nextUpalaGroup.attack(popFromMemoryArray_Hacked(newPath), bot, remainingReward - currentGroupReward);
    }

## Алгоритм 3. Надбавка
## +buffer, +0-payment, +invite-free, +price-increase
## -short

MaxBotReward определяет надбавку текущей группы. Сколько группа доплатит за бота снизу. При таком варианте без разницы, идти сверху или снизу. 

Проблема. При такой организации получится, что в верхней группе не будет единого показателя MaxBotReward. Для каждого пользователя будет свой показатель. Т.е. 
1. показатель не доступен сразу
2. он не един для группы

Для групп сверху риск в том, что их доля в награде за бота будет слишком высока.

В отличие от других вариантов, в этом алгоритме цена за бота всегда увеличивается. В крайнем случае надбавка составляет 0. 

Здесь не важна длина цепочки.









# А нужен ли score?

## Как мы определяем качество пользователя
Сейчас
у одного 80%, но за ним 1 доллар. У другого 50%, но за ним 100 долларов. Кому доверять? 
Считаем по награде за бота. 
Первый 0.8 доллара, второй 50 долларов. 
Понятно, что второй намного интереснее. НО

## Можно ли рассматривать пользователя отдельно от группы?
Нет. 
Например, один в дорогущей группе.
Возможно, это не нужно хардкодить. __Пусть это смотрят выше__. 

Можно завязать на количество пользователей, которые "доверяют" пользователю. Это, допустим, все пользователи под группой, из которой мы вычисляем рейтинг пользователя. 

Тогда может появится группа с гарантированно невзрывающимися ботами, чтобы увеличивать рейтинг. Это легко обойти тем, что можно сделать поправку на количество обычных пользователей в группе. Но это надо кодить где-то выше. Поэтому сразу нет смысла включать это в расчет. 

## Как мы определяем качество группы (с точки зрения группы снизу, с точки зрения группы сверху, с точки зрения приложения). Нужно ли приложению качество группы? 
Это награда за бота. Но если нет скоринга, а есть только награда, то у всей группы награда за бота не совсем показатель. Или показатель? 
Можно ввести какой-нибудь средний показатель(но с ним можно будет мухлевать). 

Нынешний Max bot reward тоже уязвим к этому. Можно сделать высокий бот-реворд, но давать маленький процент пользователям.

Можно, смотреть на распределение наград. Но тут тоже можно намухлевать. 
Т.е. качество самой группы как бы не важно. __Важно только качество пользователей__.  Max bot reward не показатель. 

Размер группы тоже не важен, потому что группа может нагенерить себе сколько угодно подгрупп. 












## Армии ботов
Группа может нагенерить под собой сколько угодно ботов. Возможно, ей не нужно продавать этих ботов. Она просто ими будет пользоваться. 
Такой группе даже не выгодно продавать ботов. Потому что как только она нанет продавать, ее исключат или наступит блокировка по исчерпанию пула. 
Это важный момент, похоже. 

Выгоды от использования ботов вне платформы могут оказаться выше, чем награда за ботов. 

Как это сделать? 
1. Все-таки __определить размер пула__
2. Окно на атаку (это уже запланировано, потому что все равно нужно предотвратить front-run)

Показатель качества группы. 
Если и есть боты, то им выгоднее мутить делишки за пределами группы. 

Или так. В группе 100 человек. Все в группе получают по доллару через UBI. У каждого члена группы наргада за бота 1 доллар. Тогда какие гарантии, что каждый не является ботом? 
При каких условиях владельцу ботов выгоднее взорвать ботов, чем забрать 100 долларов? Насколько больше должна быть прибыль от взрыва?
Допустим, в 10 раз.

Нет, вопрос в другом. Как определить ценность пользователя с учетом возможности формирования армии ботов. 

...

__Размер пула - это размер награды за ботнет__
Цену бота можно посчитать еще так: Размер пула/количество пользователей. Столько сможет выкачать ботнет. А как это посчитать для цепочки?

Награда за ботнет - это __цена репутации создателя пула__.

Если группа с определенным размером пула принимает другую группу, получается, она доверяет этой группе на размер своего пула. Не важно, какая цена за бота установлена. Скорее всего количества человек в нижестоящей группе все равно будет достаточно, чтобы выкачать весь пул. Т.е. размер награды за бота не важен. Еще, получается, мало важно доверие (скоринг) нижестоящей группы. 
Размер пула / (рейтинг члена * награда за бота) = количество человек с рейтингом 100%, требующееся для выкачивания всего пула. 

Группа довреят всем своим членам на размер пула. 

Размер пула говорит: "__Если под группой и есть ботнет, то он несет прибыли больше, чем размер пула__".

Важна цена репутации. 

Если группа сверху ничего не имеет в пуле, то это все равно, что просто собрать... не понял пока... 




# Примеры

## Moloch
Создали группу, скинулись по 1 доллару. Набралось 100 человек. Установили награду за бота 10 долларов. Собрали 100 долларов. Отдали на управление вышестоящей группе 50 долларов. 
Варианты: 
- Группа сверху имеет пул 5000 долларов. Доверяет этой группе, включает ее. Здесь награда за бота 20 долларов. Если Moloch явялется бот-нетом, он может взорваться и унести 500 долларов. Цена за бота при ликвидации бот-нета: 50000/100 = 50 долларов. 
- Группа сверху имеет пул 500 долларов. Остальные параметры теже. Цена за бота при ликвидации бот-нета: 500/100 = 5 долларов.

- Приложение не имеет пула. Просто доверяет группе (ну, потому что верит репутации членов). Использует репутацию напрямую. Такому приложению не нужна Upala. 

Moloch выступает как seed.
Группа скидывается, чтобы вступить в вышестоящую группу. Получается, moloch доверяет тем, кто под этой верхней группой. 

## Passport
Юзеры платят за вступление. Район платит городу, город региону. 

## Friends
Мы с друзьями скидываемся и создаем группу. Потом часть средств отдаем вверх (или разрешаем пользоваться). Т.е. мы доверяем вышестоящей группе в том, что она не пустит тех, кто захочет у нас украсть. 


Атака ботнета - это все же о репутации. Это заставит делать такую систему, в которой организатор ботнета понесет большую репутационную ответственность. Или нет? Ботнет может возникнуть более естественно. Т.е. ботоводы хакнут способ верификации в данной ветке Upala и появятся в разных группах. Потом начнут кооридированную атаку. 




## А как быть с комбинированием очков? 
Ну, этого и так сейчас не предусмотрено. 