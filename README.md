# Probability Distribution Monad

Makes it easy to create, manipulate and sample probability distributions.

For example, here's how you would code up the following problem: You are given either a fair coin or a
biased coin with equal probability. If you flip it 5 times and it comes up heads each time, what is the
probability you have the fair coin?

    def bayesianCoin(flips: Int): Distribution[(Int, List[Coin])] = {
      for {
        haveFairCoin <- tf()
        c = if (haveFairCoin) coin else biasedCoin(0.9)
        results <- c.repeat(flips)
      } yield (haveFairCoin, results)
    }
  
    bayesianCoin(5).given(_._2.forall(_ == H)).pr(_._1 == true)

Or: You repeatedly roll a 6-sided and keep a running sum. What is the probability the sum reaches
exactly 30?

    def dieSum(rolls: Int): Distribution[List[Int]] = {
      markov(rolls, List(0))(runningSum => for {
        d <- die
      } yield (d + runningSum.head) :: runningSum)
    }

    dieSum(30).pr(_.contains(30))

[Distribution.scala](https://github.com/jliszka/probability-monad/blob/master/Distribution.scala) contains code
for creating and manipulating probability distributions. Built-in distributions include:

- uniform continuous 
- uniform discrete (including die and fair coin)
- weighted discrete (biased coin, uses the [alias method](http://www.keithschwarz.com/darts-dice-coins/))
- normal
- poisson
- cauchy
- chi2

Methods for manipulating distributions include:

- adding (convolution), subracting (cross-correlation), multiplying and dividing distributions
- producing joint distributions from single distributions
- conditional distributions (amounts to a filter)
- mapping and flatMapping values inside the distribution
- creating Markov chains
- finding the probability of arbitrary predicates, conditional probabililty
- finding expected values
- sampling, histogram

[Examples.scala](https://github.com/jliszka/probability-monad/blob/master/Examples.scala) contains some 
example uses, and possibly a RISK simulator.