---
layout: blog
title: Another Ruby model
permalink: 2008/02/another-ruby-model
categories: [Code]
---

<p>class State &lt; ActiveRecord::Base<br />
  has_one :location<br />
  has_one :user<br />
  has_one :hood<br />
  has_one :option</p>
<p>  attr_reader :return_message<br />
  PRICE_OF_UNIFORM = 22<br />
  BURGER_LIMIT = 5</p>
<p>  def isactive?<br />
    (self.current_enthusiasm &gt; 0)<br />
  end</p>
<p>  def lose_enthuse!(factor = 1)<br />
    self.current_enthusiasm -= (1 * factor)<br />
    self.name = "You are visiting"<br />
  end</p>
<p>  def change_location!(location)<br />
    self.location_id = location<br />
  end</p>
<p>  def reset_enthuse!<br />
    self.current_enthusiasm = 100<br />
  end</p>
<p>  def change_hood!(hood)<br />
    self.hood_id = hood<br />
    self.location_id = 1<br />
    self.name = "Please choose somewhere to visit:"<br />
  end</p>
<p>  def set_option!(id)<br />
    self.option_id = id<br />
  end</p>
<p>  def choose_option?<br />
    s=""<br />
    @option = Option.find_by_id(self.option_id)<br />
    @thisguy=Actor.find_by_user_id(self.user_id)<br />
    if @thisguy.cash &lt; @option.cash_price then<br />
      s= s &lt;&lt; "You need at least $#{@option.cash_price}. "<br />
    end<br />
    if self.current_enthusiasm &lt; @option.enthuse_price then<br />
      s= s &lt;&lt; "You need at least #{@option.enthuse_price} enthusiasm points. "<br />
    end<br />
    if (@thisguy.has_uniform &lt; @option.uniform_needed) and (@option.give_uniform &lt; 1) then<br />
      s= s &lt;&lt; "You need a uniform for this and you can&#039;t get one here. "<br />
    end<br />
    if (@thisguy.has_uniform &lt; @option.uniform_needed) and (@option.give_uniform == 1) and (@thisguy.cash &lt; PRICE_OF_UNIFORM) then<br />
      s= s &lt;&lt; "You need a uniform for this and you can&#039;t afford one - you are short $#{PRICE_OF_UNIFORM - @thisguy.cash}. "<br />
    end<br />
    if (@thisguy.has_job &gt;= @option.give_job) and (@option.give_job &gt; 0) then<br />
      s= s &lt;&lt; "You already have a job. "<br />
    end<br />
    if @thisguy.charisma &lt; @option.charisma_needed then<br />
      s= s &lt;&lt; "You need at least #{@option.charisma_needed} charisma points. "<br />
    end<br />
    if @thisguy.skill &lt; @option.skill_needed then<br />
      s= s &lt;&lt; "You need at least #{@option.skill_needed} skill points. "<br />
    end<br />
    if @thisguy.savvy &lt; @option.savvy_needed then<br />
      s= s &lt;&lt; "You need at least #{@option.savvy_needed} savvy points. "<br />
    end<br />
    if @thisguy.looks &lt; @option.looks_needed then<br />
      s= s &lt;&lt; "You need at least #{@option.looks_needed} looks points. "<br />
    end<br />
    if @option.take_point &gt; 0 and ( @thisguy.savvy &lt; 1 and @thisguy.charisma &lt; 1 and @thisguy.skill &lt; 1 and @thisguy.looks &lt; 1 )<br />
      s= s &lt;&lt; "You have no points left to sell, you masochist. "<br />
    end<br />
    if @thisguy.fans &lt; @option.fans_needed then<br />
      s= s &lt;&lt; "You need at least #{@option.fans_needed} fans. "<br />
    end<br />
    if @option.give_charity_bar &gt; 0 and @thisguy.charity_bar &gt; 1<br />
      s= s &lt;&lt; "You already have #{@thisguy.charity_bar==1 ? "1 box" : "2 boxes"} of candy bars. "<br />
    end<br />
    if @option.charity_bar_price &gt; 0 and @thisguy.charity_bar &lt; 1<br />
      s= s &lt;&lt; "You can&#039;t sell candy bars that you don&#039;t have. "<br />
    end<br />
    #BURGER CHECK<br />
    if @option.give_burger == 2<br />
      if (@thisguy.burgers + 2) &gt; BURGER_LIMIT<br />
        s= s &lt;&lt; "You can&#039;t carry any more burgers. "<br />
      end<br />
    end</p>
<p>    if @thisguy.has_car &lt; @option.car_needed then<br />
      if @thisguy.has_car &gt; 0 then<br />
        nicer = " nicer"<br />
      end<br />
      s= s &lt;&lt; "You need a #{nicer} car. "<br />
    end<br />
    if (s.strip) == "" then<br />
      true<br />
    else<br />
      r = rand(5)<br />
      if r==1 then<br />
        @return_message="Sorry #{@thisguy.name}, you can&#039;t #{@option.name.upcase} Apparently you are not good enough right now. #{s}"<br />
      elsif r==2<br />
        @return_message="Sorry #{@thisguy.name}, you can&#039;t #{@option.name.upcase} It&#039;s not working out for you. #{s}"<br />
      elsif r==3<br />
        @return_message="Sorry #{@thisguy.name}, you can&#039;t #{@option.name.upcase} Maybe next time. #{s}"<br />
      elsif r==4<br />
        @return_message="Sorry #{@thisguy.name}, you can&#039;t #{@option.name.upcase} What a shame. #{s}"<br />
      else<br />
        @return_message="Sorry #{@thisguy.name}, you can&#039;t #{@option.name.upcase} You are coming up a little short. #{s}"<br />
      end<br />
      false<br />
    end<br />
  end</p>
<p>  def process_option!<br />
    @option = Option.find_by_id(self.option_id)<br />
    @thisguy=Actor.find_by_user_id(self.user_id)<br />
    @thisguy.cash -= @option.cash_price.to_i<br />
    self.current_enthusiasm -= @option.enthuse_price.to_i<br />
    @thisguy.cash+=@option.cash_payout<br />
    self.current_enthusiasm = (self.current_enthusiasm + @option.enthuse_payout)&gt;100 ? 100 : self.current_enthusiasm + @option.enthuse_payout<br />
    @thisguy.charisma = @thisguy.charisma + (@thisguy.charisma*(@option.charisma_quotient*0.001))<br />
    @thisguy.skill = @thisguy.skill + (@thisguy.skill*(@option.skill_quotient*0.001))<br />
    @thisguy.savvy = @thisguy.savvy + (@thisguy.savvy*(@option.savvy_quotient*0.001))<br />
    @thisguy.looks = @thisguy.looks + (@thisguy.looks*(@option.looks_quotient*0.001))<br />
    if @option.cash_price &gt; 0 then<br />
      s="You spent $#{@option.cash_price}. "<br />
    else<br />
      s=""<br />
    end<br />
    if @option.cash_payout &gt; 0 then<br />
      s= s &lt;&lt; "You made $#{@option.cash_payout}. "<br />
    end<br />
    if @option.enthuse_payout &gt; 0 then<br />
      s= s &lt;&lt; "You feel better. "<br />
    end<br />
    if @option.charisma_quotient &gt; 0 then<br />
      s= s &lt;&lt; "Your mojo is off the hook. "<br />
    end<br />
    if @option.skill_quotient &gt; 0 then<br />
      s= s &lt;&lt; "Your skill went up a bit. "<br />
    end<br />
    if @option.savvy_quotient &gt; 0 then<br />
      s= s &lt;&lt; "Your savvy has improved. "<br />
    end<br />
    if @option.looks_quotient &gt; 0 then<br />
      s= s &lt;&lt; "You just got prettier. "<br />
    end<br />
    if @option.give_car &gt; 0 then<br />
      @thisguy.has_car = @option.give_car<br />
      s= s &lt;&lt; "You got wheels. "<br />
    end<br />
    if @option.give_pet &gt; 0 then<br />
      @thisguy.has_pet = @option.give_pet<br />
      s= s &lt;&lt; "Your new pet loves you. "<br />
    end<br />
    if (@option.give_uniform == 1) and (@thisguy.has_uniform &lt; 1) then<br />
      @thisguy.cash -= PRICE_OF_UNIFORM<br />
      @thisguy.has_uniform = 1<br />
      s= s &lt;&lt; "You paid $#{PRICE_OF_UNIFORM} for your uniform. "<br />
    end<br />
    if @option.give_box &gt; 0 then<br />
      @thisguy.has_box = @option.give_box<br />
      s= s &lt;&lt; "You now have somewhere to live. "<br />
    end<br />
    if @option.give_job &gt; 0 then<br />
      @thisguy.has_job = @option.give_job<br />
      @thisguy.works_at = @option.location_id<br />
      @thisguy.last_pay_date = Time.now<br />
      s= s &lt;&lt; "You just got hired. "<br />
    end<br />
    if @option.take_point &gt; 0<br />
      whichever = rand(4)<br />
      if whichever == 1<br />
        @thisguy.skill -= 1<br />
        name = "skill"<br />
      elsif whichever == 2<br />
        @thisguy.charisma -= 1<br />
        name = "charisma"<br />
      elsif whichever == 3<br />
        @thisguy.savvy -= 1<br />
        name = "savvy"<br />
      else<br />
        @thisguy.looks -= 1<br />
        name = "looks"<br />
      end<br />
      s= s &lt;&lt; "You lost a point of #{name}. "<br />
    end<br />
    if @option.give_point &gt; 0<br />
      if @option.give_point == 1<br />
        quant = "1 point"<br />
      else<br />
        quant = "#{@option.give_point} points"<br />
      end<br />
      @thisguy.points_to_give += @option.give_point<br />
      s= s &lt;&lt; "You gained #{quant} to use however you wish. Just click on the the PDA icon by your burgers."<br />
    end<br />
    if @option.take_job &gt; 0<br />
      s= s &lt;&lt; ((@thisguy.has_job&gt;0) ? "You lost your job. " : "You got arrested for no reason. ")<br />
      @thisguy.has_job = 0<br />
    end<br />
    if @option.give_guitar &gt; 0 then<br />
      @thisguy.has_guitar = @option.give_guitar<br />
      s= s &lt;&lt; "You bought the guitar. "<br />
    end<br />
    if @option.charity_bar_price &gt; 0<br />
      @thisguy.charity_bar -= 1<br />
      s= s &lt;&lt; "You sold a box of candy bars. "<br />
    end<br />
    if @option.give_acct &gt; 0 then<br />
      @thisguy.has_acct = @option.give_acct<br />
      s= s &lt;&lt; "You will be earning #{@option.give_acct * $SAVINGS_QUOTIENT}% interest. "<br />
    end<br />
    if @option.give_burger &gt; 0 then<br />
      if @option.give_burger == 1 then<br />
        burger_count = " 1 burger "<br />
      else<br />
        burger_count = " #{@option.give_burger} burgers "<br />
      end<br />
      if (@option.give_burger == 1) and (@thisguy.burgers == BURGER_LIMIT)<br />
        s= s &lt;&lt; "You took a burger with you but it fell out of your pocket. "<br />
      else</p>
<p>        @thisguy.burgers += @option.give_burger<br />
        s= s &lt;&lt; "You wrapped up#{burger_count} to put in your pocket. "<br />
      end<br />
    end<br />
    if @option.random_money &gt; 0 then<br />
      r = rand(100)<br />
      if @option.random_money &gt; (r - (@option.random_money/2)) then<br />
        x = (@option.random_money * (r/1.6) + @option.random_money + (r * @option.random_money))<br />
        @thisguy.cash += x<br />
        s= s &lt;&lt; "You got lucky for an extra $#{(x).to_i}. "<br />
      end<br />
    end<br />
    if @option.random_pet &gt; 0 then<br />
      r = rand(100)<br />
      if (@option.random_pet &gt;= r) then<br />
        @thisguy.has_pet += 1<br />
        s= s &lt;&lt; "You seduced a stray puppie with your charm. "<br />
      end<br />
    end<br />
    #straight given fan<br />
    if @option.give_fan &gt; 0<br />
      netgain = @option.give_fan<br />
        if netgain &gt; 1 then<br />
          s_suffix = "s"<br />
        else<br />
          s_suffix = ""<br />
        end<br />
        @thisguy.fans += netgain<br />
        s= s &lt;&lt; "#{netgain} new fan#{s_suffix} came up to you after the show. "<br />
    end<br />
    #random fan<br />
    if @option.random_fan &gt; 0<br />
      netgain = 0<br />
      r = rand(90)<br />
      if @option.random_fan &gt;= r then<br />
        random_factor = 0<br />
        for i in <a href="http://www.digbox.netmailto:r..@option.random_fan">r..@option.random_fan</a><br />
          random_factor = (random_factor + i)<br />
        end<br />
        netgain = random_factor<br />
      end<br />
      if netgain &gt; 0 then<br />
        if netgain &gt; 1 then<br />
          s_suffix = "s"<br />
        else<br />
          s_suffix = ""<br />
        end<br />
        @thisguy.fans += netgain<br />
        s= s &lt;&lt; "#{netgain} new fan#{s_suffix} came to your website. "<br />
      end<br />
    end if<br />
    if @option.give_charity_bar &gt; 0<br />
      @thisguy.charity_bar += 1<br />
    end<br />
    @return_message = s<br />
    @thisguy.save<br />
    self.save<br />
  end</p>
<p>  def return_message<br />
    @return_message<br />
  end</p>
<p>  def return_message=(val)<br />
    @return_message = val<br />
  end<br />
  def take_shelter!<br />
    x = self.current_enthusiasm += 50<br />
    if x &gt; 100<br />
      x = 100<br />
    end<br />
    self.current_enthusiasm = x<br />
  end</p>
<p>end</p>
<p><a href="http://www.digbox.net/index.php/RoR/another-ruby-model">Original post</a> blogged on <a href="http://codeboxer.com">codeboxer.com</a>.</p>