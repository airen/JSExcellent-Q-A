# JSExcellent-Q-A
Ask and answer from stackoverflow

## Random

1. Get random item from JavaScript array [Link](//stackoverflow.com/questions/5915096/get-random-item-from-javascript-array)

**Q:**

How do I get random item form `items`?

    var items = Array(523,3452,334,31,...5346);

**A**:

    var item = items[Math.floor(Math.random() * items.length)];
