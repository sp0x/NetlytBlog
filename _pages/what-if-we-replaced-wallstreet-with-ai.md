---
layout: post
title:  "What if we replaced Wall Street with AI?"
date:   2019-02-25 18:27:42 +0200
categories: case-studies
category: Case Studies
description: What will happen if artificial intelligence replaces all stock brokers on Wall Street. Will the economy be more stable and fair?
cover: "/assets/wallstreet/stockmarket.jpg"
author: Tony Petrov
---

![Stock market](/assets/wallstreet/stockmarket.jpg)

We've all heard of the threat of automation and how AI will replace humans in almost every profession. Everyone from factory workers and cab drivers all the way to doctors are all under threat of losing their jobs in the next few decades according to the media. And these fears are not baseless the truth is that machines have been beating humans at every task we've thrown at them so far. 

However one job that has seemed to be immune to AI is that of the stock broker. And this got us thinking, given its super human abilities what will happen if we replaced Wall Street with AI? Is the stock market predictable? Will we see an end to the boom-bust cycles of the economy that we see today? Will we all become rich (the question that all wannabe investors want an answer to)? 

To answer these burning questions we've built the unthinkable! A stock exchange ran entirely by AI agents. All the traders in our system have been replaced by LSTM powered deep neural networks. In the following sections we'll go over the AI trader's brain and stock exchange code and finally show what happens when we turn it on!

<h2>The Stock Exchange</h2>

![The Stock Exchange](/assets/wallstreet/the_stock_exchange.jpg)

In a real stock exchange you have thousands of traders competing in a market for a limited amount of stocks for a wide variety of companies. To keep things simple our mini stock exchange will feature only 1 stock and 10 AI traders. 

Unlike real stock exchanges ours won't allow traders to place stop offers only buy and sell offers allowed! So our stock exchange runs by the fundamental laws of supply and demand. The stock exchange thus features only 3 important methods.

{% highlight python %}
    def buy(self, agent):
        if agent.cash > self.get_current_price():
            self.buy_offers.append(agent)
        else:
            self.bad_offers.append(agent)
{% endhighlight %}

Our first method is the buy method. As you can see this method features a John Spano fraud detection algorithm. If you don't have money to pay for your stock, your offer is not welcomed (NHL take note).

{% highlight python %}
    def sell(self, agent):
        if agent.qty>0:
            self.sell_offers.append(agent)
        else:
            self.bad_offers.append(agent)
{% endhighlight %}

Next on our list is the sell method. Once more our stock exchange features the latest of fraud detection systems. The anti Victor Lustig system that makes sure you actually own the thing you're trying to sell. (Yes France this code is open source)

{% highlight python %}
    def clear(self):
        cp = self.get_current_price()
        sellers = []
        buyers = []
        bad_offers = self.bad_offers
        no_offers = self.no_offers
        transactions = 0
        # while there are eager buyers and sellers left keep making trades
        while len(self.buy_offers)>0 and len(self.sell_offers)>0:
            b = self.buy_offers.pop()
            s = self.sell_offers.pop()
            # take the buyers money and calculate their reward
            rb = b.transfer(-cp)
            # give the seller his due and calculate their reward
            rs = s.transfer(cp)
            buyers.append((b, rb))
            sellers.append((s, rs))
        # increase price by 2% if more people are buying else decrease it by 2% if more people are selling
        new_price = cp * 0.02 * np.sign(len(self.buy_offers)-len(self.sell_offers)) + cp
        # record the new price for future AI generations to learn from
        self.history.append(new_price)
        # if you didn't succeed in your trade we just tell you that you need to do a better job at predicting supply and demand
        no_offers.extend(self.buy_offers)
        no_offers.extend(self.sell_offers)
        self.buy_offers = []
        self.sell_offers = []
        self.bad_offers = []
        self.no_offers = []
        hist = self.get_history()
        # notify the sellers what happened and give them their rewards
        for s,rs in sellers:
            s.observe(hist, rs)
        # notify the buyers what happened
        for b,rb in buyers:
            b.observe(hist, rb)
        # tell the hodl gang that another eon has passed and they haven't done anything productive yet
        for n in no_offers:
            n.observe(hist, REWARDS['hold'])
        # punish the cheaters
        for i in bad_offers:
            i.observe(hist, REWARDS['illegal_op'])
{% endhighlight %}

Probably the longest piece of code ever written. This is the bread and butter of our stock exchange the clear method. It's job is to match buyers with sellers and calculate the new price of Google's stock. The most complicated piece of code is of course the new price calculation itself. We increase or decrease the price of the stock by 2% depending on whether there were more buyers or sellers (higher demand means the price should go up higher supply means the stock is overpriced and the prices should go down). 

<h2>The "Intelligent" Investor</h2>

![Robot reading](/assets/wallstreet/robot_reading.jpg)

We've seen how the market works now its time to see what our intelligent investors will use to predict the trend of the market and become rich.

{% highlight python %}
    def build_agent_brain(self):
        # the state of the world aka the market trades for the last hour
        state = Input(shape=(60,1),dtype='float32')  
        # the amount of money left for the agent to spend + the price of his most expensive stock
        portfolio = Input(shape=(2,),dtype='float32')   
        s = LSTM(units=32)(state)
        s = Dense(1, activation='linear')(s)
        x = concatenate([s, portfolio],axis=1)
        x = Dense(32, activation='linear')(x)
        action = Dense(3, name='action', activation='linear')(x)
        model = Model(inputs=[state, portfolio], outputs=[action])
        model.compile(optimizer='adam', loss='mse',metrics=['mse'])
        return model
{% endhighlight %}

The code above is the neural network architecture that we'll be using. This network will help our agent navigate the treacherous waters of the stock market safely. As you can see our agent takes in 2 inputs: the state of the market (these are the closing prices for the past 60 minutes) and the state of the agent's equity (including how much money the agent has left and the highest price for a stock that it has spent). 

The state of the market is then fed into an LSTM that tries to predict what the future price will be. Then the agent takes in the prediction that it has made and the portfolio data and tries to decide whether the stock will go up or down and whether it should buy or sell. The the agent produces an output containing 3 commands: hold, buy and sell.

{% highlight python %}
    def observe(self, state, reward):
        self.mem.append((self.last_state, self.last_action, 
                               self.last_cash_balance, self.last_reward, 
                               self.max_purchase_price))
        action = self.action(state)
        if action == SELL:
            self.stock_exchange.sell(self)
        elif action == BUY:
            self.stock_exchange.buy(self)
        else:
            self.stock_exchange.nop(self)
        if len(self.mem)>=MEM_SIZE:
            self.learn()
        self.last_action = action
        self.last_reward = reward
        self.last_cash_balance = self.cash
        self.last_state = deepcopy(state)
{% endhighlight %}

The observe method is where our agent gets its inputs from. This method is called once the stock exchange has cleared all trades. It notifies the agent what the state of the market is and what rewards the agent has received for its actions during the previous trade cycle. Once the agent has updated its internal state it sends out a new command to the market. 

{% highlight python %}
    def learn(self):
        for s,la,lcb,r,mp in self.mem:
            s = np.array(s).reshape(1,-1, 1)
            p = np.array([mp,lcb]).reshape(1, 2)
            a = self.brain.predict([s,p])
            target = r + GAMMA * np.max(a)
            a[0][la] = target
            self.brain.fit([s,p], a, epochs=1, verbose=0)
        self.mem = []
{% endhighlight %}

The learn method is what separates our agent from the common trader bot. Our agent uses replay memory which allows it to "relive" its previous actions and see how they've affected the world. For each action we update the agents utility function (this is what guides the agent's behavior). Then we fit the agent for 1 epoch. 

{% highlight python %}
    def action(self, state):
        self.eps *= DECAY
        if np.random.rand() < self.eps:
            a = np.random.randint(0, 2)
        else:
            s = np.array(state).reshape(1,-1,1)
            p = np.array([self.max_purchase_price, self.cash]).reshape(1,2)
            a = self.brain.predict([s,p])
            a = np.argmax(a)
        return a
{% endhighlight %}

The action function is what our agent uses to decide what to do after each stock clearance. For our decision process we use the ε-greedy algorithm. In a nutshell we randomly decide whether to listen to our agents decision or to pick a new decision for him. The idea is to have the agent "explore" the world before he can make his own decisions. 

To prevent our agent from becoming unpredictable we use a decay factor. The decay factor reduces the probability of a random action taking place. In practice after 60 trades our agent is completely autonomous and makes its own decisions.  

{% highlight python %}
    def transfer(self, price):
        self.cash += price
        self.qty += -np.sign(price)
        earnings = price - self.max_purchase_price
        if self.qty == 0:
            self.max_purchase_price = 0.0
        elif np.sign(price) == -1: # agent has bought more stock      
            # get the highest price that the agent has bought at since it'll dominate his expenditures and thus determine his earnings      
            self.max_purchase_price = max(self.max_purchase_price, -price) 
            return 0.0001
        return earnings
{% endhighlight %}

Finally lets take a look at the transfer function. This function helps the stock exchange facilitate trade and calculate rewards. Our agent can either receive a small reward for buying (analogous to a dopamine injection when a stock broker buys stock). 

Or we simply return the difference between the sell price and purchase price of a stock when the agent decides to sell. We store the highest purchase price as any future trade will have to earn more than this price in order for the agent to make money. 

Ultimately our agent will try to maximize its sell price and minimize its buy price. In broker terms our agent is buying low and selling high.

<h2>Wall Street Out AI In</h2>

![AI takes over Wall street](/assets/wallstreet/ai_news.jpg)

Now that everything is ready it's time to let our agents take over the stock exchange. For our tests we'll spawn 10 agents (5 buyers and 5 sellers). In the first experiment our buyers will have $1,000 each and our sellers will each have only 1 share. The starting price is at $782.75 and we give the agent the data for the previous 60 transactions so it can start learning. Remember this is a stock exchange and our bots represent investors who are trying to make enough money for retirement. So we want this market to go up!

![Stock Exchange Simulation with Default conditions](/assets/wallstreet/rl_run1.png)

*Fig1. Stock market simulation with $1,000 accounts. Y-axis shows the price of the stock in thousands of dollars. X-axis represents time.*

After the first simulation it seems that our market has crashed. Looks like no one will be retiring anytime soon. But how can this be? Shouldn't our agents be smart enough to find the fair price and trade happily ever after? 

Well our agents have actually found a fair price which is slightly above 0. The reason for this is that our trader agent gets punished if it doesn't make a profit. To make a profit the agent has to sell at a price higher than the purchase price. 

However our buyer agents start with only a few hundred dollars more than the open price. This makes their margins extremely low and they refuse to buy. So what can we do to fix this? We could give our bots an extra $1,000 in the form of credit this is similar to a margin account.

![Stock Exchange Simulation with 2x credit](/assets/wallstreet/rl_2xcredit_run1.png)

*Fig2. Stock market simulation with margin accounts*

Our crashing problem has been solved! The agents have found a new stable price at around $1,900. But why did the bots go into a shopping spree? The reason for this is that the margin account has made money worthless by minimizing losses. 

When the agent runs out of money it doesn't get punished instead it takes a loan and can buy as much stock as it needs. The amount of shares on the other hand is still limited hence why shares become more valuable. 

In this situation the sellers are reluctant to sell their precious shares for worthless cash. So what will happen if we increased the amount of shares? Time for a stock split!

![Stock Exchange Simulation with 5x number of shares](/assets/wallstreet/rl_5xqty_run1.png)

*Fig3. Stock market simulation with 5x the number of shares*

Increasing the number of shares has made them worth less than the money that the agent has. But why is the stock price swinging back and forth? To answer this question first need to see what will happen if we give our agents more money. Yes we're talking about quantitative easing! How will the market respond?

![Stock Exchange Simulation with 5x number of shares and 5x amount of credit](/assets/wallstreet/rl_5xall_run1.png)

*Fig4. Stock market simulation with 5x the number of shares and 5x the amount of credit*

Interesting after a few ups and downs it looks like we've finally entered a bull market. Prices are rising and our bots are becoming richer than ever! So all we needed to do is supply more money and stock? Not quite let's have a look at what happens to the market in the long run.

![Stock Exchange Simulation with 5x number of shares and credit in the long run](/assets/wallstreet/rl_5xall_run2_4k_iter.png)

*Fig5. Long run stock market simulation with 5x the number of shares and 5x the amount of credit*

The market has crashed yet again. However if you take a closer look you'd notice something very interesting. The market seems to have undergone a series of boom-bust cycles similar to our 3rd simulation. This time the price kept going up in the long run before suddenly crashing. So how can we explain this to our retirees who lost all of their money? How and why does this keep happening?

![Game Theory](/assets/wallstreet/game_theory.png)

The explanation is rather simple and it comes from Game Theory (no not the YouTube channel [this Game theory](https://en.wikipedia.org/wiki/Game_theory)). In game theory there's the notion of zero-sum games. A zero-sum game is a type of game in which in order for one player to win the other one must lose. 

I can already hear economists yelling "Trade is not a zero-sum game!". And they're right normal trade is not a zero-sum game, but day trade is. Why? Because our bots (and real world day traders for that matter) don't really want the stock. They're just buying it in order for them to sell it in the future at a higher price. 

The person/bot getting in on the stock last is the one that all previous traders profit off of. In order for the last trader to get his money back someone else needs to pay him an even higher price! This is contrary to regular trade where the buyer exchanges his money for something he wants. 

In a regular trade the consumed good rarely gets resold countless times at a higher price. The utility of a product comes from the product itself and its consumption. In day trade the utility comes from the reselling of the stock. So what does this all mean?

![Tug of war](/assets/wallstreet/competition.jpg)

In layman terms our agents are locked in a tug-of-war with both sides being equally strong. Buyer bots are trying to pull the price as low as possible in order for them to maximize future profits. 

Seller bots are trying to push the price as high as possible to take away as much money as possible from the buyers. With every agent trying to maximize his earnings and minimize his losses we end up with a market that oscillates between booms and busts like the one in Fig3. 

You may be wondering but if the bots are pulling with equal force why do we get higher prices when we add more money?

![Credit](/assets/wallstreet/credit-pull.png)

The sharp price rises are caused by imperfect information. Simply put this means that our seller bots don't know how much money the buyers have(otherwise they'd just set the price to that number and take everything away from the buyers). 

In the beginning prices go sharply up as the sellers try to estimate how much money the buyers have. Once the maximum amount of money that a buyer has is known (meaning buyers stopped buying) prices go sharply down in an attempt to maximize the profit of a future sell. 

This is what you see in Fig1. But why does the market keep going up to ever higher prices in Fig5? The reason for this is credit. We didn't give money to our buyers directly. Rather we allowed them to borrow more on top of what they have. This creates the illusion that there's more money in the system than there actually is. 

When a seller makes a profit, and has collected all the money he can, he becomes a buyer. However now he has his profits + the amount of money that he can borrow on top of that. Once this bot starts to buy the others respond by pulling the price up until he runs out of money and credit. 

Then the new price becomes the money collected from the previous boom + the credit. But why don't the oscillations continue indefinitely? Why is there a sudden crash at the end and no recovery, real world markets always recover?

![Incentives](/assets/wallstreet/incentives.png)

The reasons why real world markets never crash like ours are many but can all be summed up by one word: incentives. Incentives are used to guide people's (and our bots') behavior. In our little market the only reason our bots buy stock is to sell it at a higher price. 

Profit is their only incentive. This means that the stock itself has no real value to the bot. The bot would rather have money than stock. Once the bot has sold at the highest possible price, he assumes that he has won and thus let's the market plummet by holding on to his money out of fear of losing it. 

In the real world a company's stock is more than an instrument for reselling. It's a share of a real world business, with real world assets that can generate income for their owner in the form of dividends. This actually makes stocks valuable in the real world and when we see a sharp fall in prices value investors see this as an opportunity to buy $1 shares for $0.50. 

Furthermore pension funds are heavily invested in the real world markets. If prices start to fall governments are compelled to intervene and protect the pensions of their elderly citizens.

<h2>Conclusion</h2>
In our attempt to improve the stock market with AI we've managed to make things worse! We saw that an AI can predict the trends of the stock market and make trading decisions on its own. 

Our agents bargained and traded like real humans but ultimately failed to distribute the wealth equally. We could not put an end to the boom-bust cycle but we've gained insights as to why it occurs. We've seen the power and importance that incentives play in the real market. 

Aiding recovery and causing busts at the same time. We saw the effect of credit expansion and share splits on the market. By increasing the amount of money and stock we've managed to extend the length of the boom-bust cycle. Resulting in ever increasing gains when the market went up and even bigger losses when it inevitably fell down. 

And to answer the most important question of them all. If we all use AI to trade for us on the stock market will we become rich? 

![Wealth distribution](/assets/wallstreet/wealth_distribution.png)

*Wealth of all 10 bots after the end of simulation 5*

To get the full code visit: [https://github.com/2087829p/wallstreet-ai/](https://github.com/2087829p/wallstreet-ai/)
