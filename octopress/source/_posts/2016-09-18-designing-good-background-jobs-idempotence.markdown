---
layout: post
title: "Designing Good Background Jobs: Idempotence"
date: 2016-09-18 10:13:50 +1000
comments: true
categories: coding design 
---

## What are Background Jobs?

Background jobs are almost part and parcel of every modern Web applications now-a-days. It's the process of
deferring time-intensive operations in the background and sending early feedback to end users. Examples of
background jobs could be generating reports, processing large amount of data, sending scheduled emails and
doing any other kind of works which are necessary but not worth blocking the otherwise quick response to
waiting user. I can go on forever with examples of background jobs, candidates for background jobs are
omnipresent (everywhere).

<!-- more -->


## What is an idempotent operation?

In mathematics, an idempotent opretation (i.e. functions in programming lanauges) is one where `F(F(x)) = F(x)`.
So you can see, no matter how how many times you apply the functions to the same argument it's equivalent to
applying it only once.

What are some idempotents functions in our day to day programming? There are many, almost every langauges we
work with some kind of `abs` (absolute) function, which gives you the abosolute value of it's argument.

It's an idempotent operation becuase `abs(abs(abs(-10)))` is equivalent to `abs(-10)` and the result will always be `10`.
Other examples include, Sorting a list of values, transforming a string to all uppercase charecters, Capitalize the first
letter of a string and many others.

## Why idempotence can be a good property of a background job?

In any sufficiently large web application, there could be lots of background jobs of all sorts. And things fails and fails for
no obvious reasons. Specially, when you have to interact with other systems you have no control over. You can pull your hair when
you get a `.NET` exception as the response with a `200` response code for a day. You look at whatever monitoring system or background
jobs console your system have the next day, and you have myriad of failed Jobs.


What do you do? Do you go through all of them one by one
and fix them one by one. Can you just retry all of them without thinking about them? It appears that you can, if your jobs are `idempotent`
as a whole. The scenario above is just one of many reasons your jobs could fail. You `database` server could die on your face. You could get
`dogshit` from another api while your jobs were expecting other things. And as your language is `RAD` and `Dynamic`, uses dynamic dispatch it
doesn't care what you are sending where until it's executing it and things fail on your face.

If your job is idempotent, you can be sure that your job is not in an inconsistant state and you can confidently retry all of them.

## What idempotence means for a background job?

Idempotence can mean all sorts of things, like for a `SendOrderToWarehouse` job it could be `was the order already shipped?` or for a
`CapturePaymentJob` job it could be `was the payment already captured?`. Ideally the you will be talking through some kind of API for these
actions, so you could think the other end of the communication should make sure these operations are idempotent. But in real world, the best idea
is to not assume anything about other systems. Common sense is not that common.


## Implementing idempotence into a background Job?

Idempotence could be easily implemented as some kind of `status` in a persistant storage. If you have a `Order` record, it could have a boolean
field named `shipped` which is only set to `true` after you get successful response from the `Warehouse` that it has imported the `ShippingRequest`
into it's system.

Let's implement two sample classes to simulate how `idempotence` can be implemented in a Job. Code examples are in `Ruby`

```ruby Warehouse
class Warehouse
  # it should be used as a singleton for the sake of this example
  def self.the_warehouse
    @the_warehouse ||= Warehouse.new
  end

  def initialize
    self.shipments = Hash.new { |hash, key| hash[key] = [] }
    self.counter = 0
  end
  
  # it keeps track of already shipped orders via a hash 
  # (which is an instance var and will persist if the class is used as a singleton)
  def shipments_for(order_id)
    shipments[order_id]
  end

  def ship_order(order_id)
    shipment_id = next_shipment_id
    shipments[order_id] << shipment_id
    shipment_id
  end

  private

  attr_accessor :shipments, :counter

  def next_shipment_id
    id = format('S%06d', counter)
    self.counter = counter + 1
    id
  end
end
```

Now lets implement a simple `ShipOrderJob`

```ruby ShipOrder
class ShipOrderJob
  def self.perform(order_id)
    new(order_id).run
  end

  def initialize(order_id)
    self.order_id = order_id
  end

  def run
    if shipped?
      puts "Order with id# #{order_id} has already been shipped. Skip processing."
      return
    end

    warehouse.ship_order(order_id)
  end

  private

  attr_accessor :order_id

  def shipped?
    !warehouse.shipments_for(order_id).empty?
  end

  def warehouse
    Warehouse.the_warehouse
  end
end
```

Now if we enqueue `ShipOrder` with the same `order_id` multiple times (one wouldn't enqueue a single job multiple times willingly,
but we could require re-enqueueing them if they fails) after the first instance is executed, it will just ignore all others
with a Log.

Idempotence is not always easy to implement, because in a Complex system a Job can enqueue other jobs and can do all sorts of
complex stuffs.

But the thing is, one should strive towards designing Idempotent Jobs.

N.B. I learned all of this from a Internal Course from of my Company, the the credit goes to the Original creator of the course. 


