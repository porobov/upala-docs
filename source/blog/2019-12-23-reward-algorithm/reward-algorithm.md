Let's classify
There are two things we need to decide on: the scoring and the bot reward algorithm. These two may be connected or they may not. 

Lets categorize the scoring algorithm. 

maxBotReward may be
1. present
The score is MaxBotReward x PercentOfTrust
2. not present
The "price" of a member is the score. 
3. maxBotReward = pool size
The score is Pool Size x PercentOfTrust

PercentOfTrust
1. Equals or relative to stake
2. Is set by admin. 


# ALGORITHM REQUIREMENTS
**Bring real world trust to Ethereum blockchain**

Share risks and income. 
__Invest trust, recieve score.__

- a member with high score can enter a cheap group, but still remain high score
- initiative to deposit into a higher groups than into own one (move trust up the path, get more score)
- get more bot reward than it is deposited

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
## everybody pays


#  Invest trust, recieve score.
Let's try to break this down. 

We would like higher groups to manage/allocate trust in a way that provides the highest scores to users. 

Trust is allocated deposit from below. Score is bot reward. 



# Примеры
## Алгоритм 1. Максимальные выплаты сверху.
## +invite-free, +buffer, +0-payment, +short
## -everybody pays

Вычисляем bot_reward в верхней группе:
    bot_reward = max_bot_reward * user_score
В этой группе выплачиваем current_bot_reward: 
    current_bot_reward = bot_reward - bot_reward всех нижестоящих. 

## Алгоритм 2. Максимальные выплаты снизу.
## +buffer, +0-payment, 
## -invite-free, -everybody pays

    function attack(address[] calldata path, address payable bot, uint remainingReward) external onlyMember {
        // calculate maxBotReward and payout the sum starting from the bottom. Stop when the sum is payed.
        uint currentGroupReward = maxBotReward * membersScores[msg.sender];  // todo is this simplification correct?
        IUpalaGroup nextUpalaGroup = IUpalaGroup(path[path.length-1]);
        
        _rewardBot(bot, currentGroupReward);
        
        address[] memory newPath = path;
        nextUpalaGroup.attack(popFromMemoryArray_Hacked(newPath), bot, remainingReward - currentGroupReward);
    }

## Алгоритм 3. Надбавка
## +buffer, +0-payment, +invite-free, +price-increase, +everybody-pays
## -short

MaxBotReward определяет надбавку текущей группы. Сколько группа доплатит за бота снизу. При таком варианте без разницы, идти сверху или снизу. 

Проблема. При такой организации получится, что в верхней группе не будет единого показателя MaxBotReward. Для каждого пользователя будет свой показатель. Т.е. 
1. показатель не доступен сразу
2. он не един для группы

Для групп сверху риск в том, что их доля в награде за бота будет слишком высока.

В отличие от других вариантов, в этом алгоритме цена за бота всегда увеличивается. В крайнем случае надбавка составляет 0. 

Здесь не важна длина цепочки.

## Алгоритм 4. Делим пропорционально.
## +everybody pays
##

Берем maxBotReward * score и распределяем его по всей цепочке пропорционально maxBotReward в каждой группе. 

## Algorithm 5. Bot reward is the score
The higher the group the more the reward
Payments start from the bottom.




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

# Для чего может быть нужен (maxReward vs no max reward)
Сочетания скоринга и maxBotReward (или MaxAddedBotReward) дает понятный максимальный показатель. Если этого не будет, максимум будет неопределен. ????




# Надбавка vs фиксированная плата
The question is whether a super groups adds to a score, or should it just fix the cap. 
With the fixed cap a very expensive account has no initiative to enter a cheap group. 

**Fixed cap does not define the quality of a group**. Admins can easily set user scores to near 0. What is the necessity of using it then? Is it the pool size to user count ratio?
                pool size
      quality = .......... ?
                user count
**Pool size does not define group quality either**. Admin can create a super-identity and give high score to it. 





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

Explosion (E) - цена за уничтожение всех пользователей
Pool (P) - размер пула
Botnet reward (B) - награда ботнета за взрыв.

Members | Bot reward  | Mass explosion reward   | Botnet reward per bot   Botnet reward           
10  20  200 20  200 P > E    E = B   P > B
25  20  500 20  500 P = E    E = B   P = B
100 20  2000    5   500 P < E    E > B   P = B

Что значит Pool = Explosion?
Цена за бота может быть выше или ниже bot reward, установленной в группе. Что это значит? 

Все это можно считать только отностильно группы. Так же, как и в случае с пользователями. Определяем сколько стоит ликвидировать ботнет. 

# С точки зрения соседской группы. 
Вот, я вижу, что моя верхняя группа добавляет группу. Что мне важно. 
- Может ли эта новая группа выкачать весь пул. С моей точки зрения это зависит только от количества пользователей в ней. 
- Доверяют ли этой группе другие группы. Может ли эта группа получить больше денег, если взорвется по другой ветке? Сколько ей уже доверили? 
- Смотрю ли я на цену бота в группе? 

# С точки зрения верхней группы (включать или нет)
Решаю, добавить ли группу к себе
- Может ли выкачать весь пул? - все пункты выше, потому что это одно и то же. 
- Если есть подозрительная группа, то есть ли над ней буфер? 

# С точки зрения приложения


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


## Staking with percent of group's pool. 
maxBotReward = Pool size

Then score is the percent of the pool. 

If the pool gets attacked, everybody loses money AND reputation (score).

Than the question whether to use added value or fixed value answers itself. We can only create added value in a setup like this. 

Now it makes sense to stake oneself. Staking like this will increase user's score and signal that the user trusts their peers. **The score is a share now.** BUT. There must be now a constraint that incentivizes users to stake higher and higher groups instead of themselves. 
Probably no constraint needed. The higher the group, the less members it has. The more percents it can give to members. Maybe this is a constraint - share the whole pool. That means that scores are represented by shares, not percent. A new member getting a share dilutes all other shares. 

## Mind where the capital comes from
What if a group have it's own capital. Then it has power to asses it's members. **Without own capital the scores will just mirror stakes (shares).** 

## Does the real world reputation matter?
What if a group has a leader with real world reputation. It is in their interest to allow this leader to assign scores.

**This setup will decrease bot reward with every explosion.**



# Scenarios and roles

What we should encourage and what discourage. 
## Bringing capital into a group

**Pool owners control scores, share fees and bear bot risks.**

### A member brings capital. It means:
- The member increases it's score (encourage or discourage?)
- The member trusts other members (encourage)

### New member brings capital 
- Lowers risks for others (if they trust the new member)
- Increases scores (how?)

### An app brings capital to a group

### Investor (curator) brings capital
- It wants to earn with the group. The entity trusts the group, stakes on it and earns (encourage). Reward - shares from apps fees, Risk - bot attacks. Users are interested in investors to increase their scores. 


# Parties

## User
Wants to select the best performing groups. And "invest" (whatever it means for now) in them. 
What is best preforming:
- Trusted by the user
- Trusted from above
- Working hard to select best groups above (allocate capital in the best possible way)
- Best earning opportunities
- Lowest fees (if any at all)

## Group 
Want to have high quality members 

## Apps 
Want to select best performing groups:
- High people count
- Highest scores 
- Lowest fees





# Metrics (all possible)
Invest trust, recieve score.

## s. Price-of-personhood. Score
How valuable the ID is to the owner 
How expensive it is to get another one 
Doesn't depend on deposit, but rather on future work and value of the ID. Thus deposit is unnecessary. 
By encreasing the price we bargain with bots, allure bots to surrender. 

## P. Pool size
If Botnet reward is not used, pool size shows group's trust to all of its members. 

If we decide to use shares group pool will consist of shares (but it could be obsolete in this case). 

## N. Number of members
Doesn't make much sense all by itself. Only if we introduce some additioal constraints like a fixed number of users per group. 

## n. Number of users
The users, the riskier it is to add group as a member. Introducing Bot-net reward relieves this risk a bit.
n is also needed to calculate individual bot reward, if we stick with the Bot-net reward. Though we can decouple individual bot reward from number of users.



## r. Bot reward for a individual
Could be calculated as R/n. But it is hard to keep track of users at leat with the current smart contract. 

What are the benefits of decoupling it from nuber of users except for optimization?
We can then use it as a characteristic of individuals within the subgroup. 
What if the corresponding member decides on the reward? Makes sense. This parameter shows **how much subgroup members trust each other**.

If not bot-net reward used this shows the overall groups trust to the member. The member can drain the whole pool. 

Or 

**Could be set by the topmost group**
Signals of the group quality (everyone underneath is equal).



## R. Bot reward for a member (bot-net reward)
Could serve as a harm limit. 
Shows the overall member reliability (is it a bonet or not). **How much the group trusts the member**.

This should be higher than the member's deposit (if anything deposited at all). And it is ok if sum of all botnet rewards exceeds the pol size

Looks very much like a stake. 







## d. Member deposit into a group (d)
By contributing deposit into a group the **member signals of its trust to the group**.
The more the deposit the more the trust. 
From the group's side the picture is reverse: the group will try to compensate for risks of letting a new untrusted member in by demanding higher deposit.

This could also be viewed as shares. For example a lower group may invest in a number of higher groups. 

User deposit may be virtual. It will affect future income (exposure to risks)


## Trust multiplier. 
If we link deposit and Botnet reward, we could use this multiplier as a substitute for Botnet reward. It will effectively show group's trust to the member. 




## i. Investment into a group (stake?)
What if a thrird-party wants to invest into a group. What are the motifs of doing so? 
Donation. 
A donor would like to support a weak group and let it be accepted. The donor then bears risks of explosions from that group. 
Investment. 
An investor will have a share in that group. So that they could earn. 

Interesting question is what we will do if we link Botnet reward to member's deposit... Let the groups decide, what ratio of income to risk exposure they are willing to give. 

__A special kind of earning shares?__




## Comission for joining a group. 
A group may earn by charging a comission. The comission does not affect pool. If a group requires a fee from a member, it decides which part is to be forwarded further up and which to be collected as fee. 

## Comission for inviting a group 

Investments and commission could be added afterwards. Could be done in parallel. 







# Unions vs Layer 2 Analyzers.

Suppose there are github users dimension and facebook users dimension. The union will have to be created as a separate group, which will check every user's score in both dimensions and assign score accordingly. So Union becomes new dimension. This is Layer 2 analyzer described. 

Now let's try to compare. 

Union unites two different sets of people, whereas analyzer unite same people. 



# Option. Max Bot reward is asigned to a group member (not to a end-user). 
If the group makes a deposit, the bot reward could be a multiplication of it. This is how trust is built. 

What I like about this option is that bot reward is assigned in the same way at any level of group hierarchy. In other setups the bottom-most groups assign rewards to members (as members = users), while all other levels assign to users. 

Another thing that I like. Here the harm is restricted. And bot reward is easier understood. And there is a simpler description for the bot reward. It is bot-net reward. 


Now, if we want to connect bot reward to member deposit what is the best way to do it? 
We can introduce a trust multiplier. The bot reward (or rather bot-net reward) in this case would be equal deposit x multiplier. 


# Option. Free but limited reputation for all users. 

Give free tokens to avery new user. The amount is fixed and non-transferable. A user can only allocate tokens in groups. 

Groups allocate their tokens in the same way. __So this is trust from bottom.__ 


# Option. Shares of shares, tokens of tokens. 

How a bot reward is then calculated? First, let's think how trust descends. Every group assigns scores to users. This is the reward. Is there a connection between the reward and user/member's deposit? There may be no such correlation. This is market. 
" I can give you this score, but you need to deposit that much". So even with no value behind trust token, users shape the market. They chose. Sure, but doesn't it make even more sense to couple trust and score. Here trust multiplier comes in. 
"I'd better select groups with high trust multiplier, but I should always be ware of risks". __Opportunity and risk__. 
Makes sense. __Invest trust, recieve score.__ Both can be represented in dollars. 
The investment may earn as well. If the top group produces some value (scoring fees, UBI, etc.)
How can groups earn from the bottom? Make it the same way? 
"I'd better select members with the highest gain, but I need to select only trusted ones". Opportunities and risks. But higher groups "allocate/invest" in their members more money than they possess due to the trust multiplier. 

By the way, in the shares model __all the money may be stored in the topmost group__. Every member below has shares, so there is no need to calculate anything. Just transfer real money to the bot. Everybody loses automatically. 

Maybe groups and members just exchange shares? Cannot figure out. 

Channels. We don't have to align profits with trust and scores (all that stuff). __A profitable share is a bonus__ (there is no need to descend it down to the user level). Let dimensions decide on that. Same with bottom-top income. It is a bonus. 

If a group is profitable, let anyone invest in the profits. Let profits be independent of bot reward mechanism. s

Here a very big problem underlies. When buing shares of a bottom group, the price needs to be calculated everytime. Shares spread up in a tree-like structure. __We'd have to analyse the whole tree__. 


Reputation = Cost + margin