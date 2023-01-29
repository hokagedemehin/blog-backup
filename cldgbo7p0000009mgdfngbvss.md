# Converting a number to a short human-readable format

This blog aims to provide simple reusable functions you can use in your projects to build user-friendly web applications or mobile applications.  
  
This one does a straightforward job of converting a number it receives to the shortest human-readable form

```javascript
function convertAmount(amount) {
  const units = ['k', 'm', 'b', 't'];
  if(amount < 1000) return amount;
  let convertedAmount = amount;
  let step = 0;
  while (convertedAmount >= 1000) {
    convertedAmount /= 1000;
    step += 1;
  }
  return `${convertedAmount}${units[step - 1]}`;
}
```

To begin, we have an array of letters that we will use to replace the given number based on the number of zeros it has.

First, we check if the number is less than a thousand, if so we do nothing and return the number.

But if it is greater than 999, the next block of code will run where we have a variable `convertedAmount` keeping track of the amount we are converting and a variable `step` keeping track of the letter we will use in the units array and finally the while loop will run and perform the following task

* divide the `convertedNumber` by 1000 and increase the `step` by 1.
    
* once the `convertedNumber` is less than a thousand, the loop stops and the `step` value will be used as the index value for the units array.
    

The final result will be a template string that joins it all together and gives you exactly what you need.  
  
What do you think?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674932390239/595f822d-0e32-4bc5-abf0-452a459c4ab9.png align="center")