### Underflow Example

```
 require (balance - withdraw_amount > 0);
```
This transaction should fail and produce an error because not enough funds are held within the account for the transaction. But what if we have 5 dollars and we withdraw 6 dollars using the scenario above and our variable can hold 2 digits with an unsigned integer?

5 - 6 = 99
```
require (99 > 0) 
```
example of vulnerable withdraw functions
```
    function withdraw(uint _amount){
           require(balances[msg.sender] - _amount > 0);
           msg.sender.transfer(_amount);
                   balances[msg.sender] -= _amount;
    }
```
### Batch Overflow

Transfer Function Vulnerable to a Batch Overflow

For example, if your integer can only hold 5 digits in length or 00,000 what would happen in the below scenario?

You have 10,000 tokens in your account

You are sending 200 users 499 tokens each

Your total sent is 200*499 or 99,800

 

The above scenario should fail, and it does, since we have 10,000 tokens and want to send a total of 99,800. But what if we send 500 tokens each? Let’s do some more math and see how that changes the outcome.

 

You have 10,000 tokens in your account

You are sending 200 users 500 tokens each

Your total sent is 200*500 or 100,000

New total is actually 0

 
```
    uint total = _users.length * _tokens;
    require(balances[msg.sender] >= total);
    balances[msg.sender] = balances[msg.sender] -total;
   
   for(uint i=0; i < users.length; i++){ 
                   balances[_users[i]] = balances[_users[i]] + _value;
```

or

```
    uint total = _200 * 500;
    require(10,000 >= 0);
    balances[msg.sender] = 10,000 - 0;
   
    for(uint i=0; i < 500; i++){ 
                   balances[_recievers[i]] = balances[_recievers[i]] + 500;
```

1: The total variable equals 100,000 which becomes 0 due to the 5-digit limit. When a 6th digit is hit at 99,999 + 1 the total now becomes 0.

2: This line checks if the users balance is higher than the total value to be sent. Which in this case is 0 so 10,000 is more than enough and this check passes due to the overflow.

 

3: This line deducts the total from the sender’s balance which does nothing since the total of 10,000 - 0 is 10,000.  The sender has lost no funds.

4-5: This loop iterates over the 200 users who each get 500 tokens and updates the balances of each user individually using the real value of 500 and this individual action does not trigger an overflow condition. Thus, sending out 100,000 tokens without reducing the sender’s balance or triggering an error due to lack of funds. This is essentially creating tokens out of thin air.

In this scenario the user retained all of their tokens but was able to distribute 100k tokens across 200 users regardless if they had the proper funds to do so.
