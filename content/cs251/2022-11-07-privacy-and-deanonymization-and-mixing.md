# Privacy, Deanonymization, and Mixing

## The need for financial system privacy

* Supply chain privacy: a manufacturer doesn't want to reveal how much it pays its supplier for parts
* Payment privacy: company that pays its employees in crypto wants to keep list of employees and salaries private
    - End users need privacy for rent, donations, purchases

However: bare usage of blockchain results in thransactions being public to everyone!

## Types of privacy

* Pseudonymity (weak privacy)
    - Every user has long term consistent pseudonym (e.g. Reddit)
        - Pros: reputation
        - Cons: link to real-world identity can leak over time
* Full anonymity: user's transactions are unlinkable
    - Cannot tell if two transactions are from the same address

### Privacy from who?

* No privacy: everyone can see all transactions
* Privacy from the public: only trusted operator can see tranasactions (e.g. Visa, Mastercard)
* Semi-full privacy: only "local" law enforcement can see transactions
* Full privacy: no-one can see transactions (e.g. TornadoCash)
    - Negative aspects: how to prevent criminal activity (supporting positive applications, but prevent the negative ones) and ensure legal compliance:

## De-anonymization strategies

Strategies for deanonymizing Bitcoin addresses

* Heuristic 1: 2 addresses input to a Tx: both addresses controlled by the same entity
* Heuristic 2: change address is controlled by the same user as input address

## Private coins on a public blockchain

* Attempt 1: simple mixing
    - Transaction inputs: users send Tx to mixer, mixer generates fresh addresses and sends Tx to intended recipients
    - However: mixer knows the shuffle, and can abscond with the money
* Attepmt 2: mixing without a mixer
    - CoinJoin (BTC)
    - TornadoCash (ETH)