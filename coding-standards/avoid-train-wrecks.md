# Avoid Train Wrecks

When designing classes, create methods that handle their responsibilities in a single call, rather than using ["train wreck"](https://wiki.c2.com/?TrainWreck) function chaining.

```ts
// Bad
client.getMortgage().paymentCollection().getNextPayment().applyPayment(300);

// Good
client.applyMortgagePayment(300);
```
