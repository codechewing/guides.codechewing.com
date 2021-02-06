# Sort an array of numbers in order with JavaScript

Sorting algorithms are a way of organising an array of numbers into a new desired order; such as **sorting numbers
in ascending order** from a unorderly array can be quite useful.

To do this in JavaScript, we can easily take advantage of the `sort()` method, and never worry about how it may have
got to the end result:

```
const array = [2, 1, 4, 3];
array.sort(); // [1, 2, 3, 4]
```

But sometimes it's useful (and enlightening) to gain a deeper understanding of what it takes to create such a
sorting algorithm yourself.

## Bubble sort with JavaScript

Here's a simple enough bubble sort from scratch, using JavaScript
([live demo](https://codepen.io/anon/pen/zAqxE)(rel="nofollow noopener noreferrer" target="_blank")):

```
// Unsorted array
const array = [1, 4, 27, 3, 2];

// Make a copy of the original array
const sortedArray = [].concat(array);

// Sort array (ascending)
function sort() {

  // This swapped 'flag' tells the function whether or not it will
  // need to iterate over the array again to continue sorting
  let swapped = false;

  for( let i = 1; i < array.length; i++ ) {
    const prev = array[i - 1];
    const current = array[i];

    // If the previous number is > than the current, swap them around
    if( prev > current ) {
      swapped = true;

      sortedArray[i] = prev;
      sortedArray[i - 1] = current;
    }

  }

  // If there has been a swap, sort over the array again
  if( swapped ) {
    return sort();
  }

}

// Run the sort function
sort(); // [1, 2, 3, 4, 27]
```

Hopefully the comments can guide you comfortably through this example.

Note; There's a variable called `swapped`, which gets set to `true` or `false` - it lets the `sort()` function
know whether or not to call itself again. Eventually, they'll be no more swaps to make, so `swapped` gets set
to `false`, ending the recursion.

### Farewell

'Remaking' these types of sorting algorithms makes for rewarding learning, and it will make you a better
developer for appreciating and understanding what goes on behind the scenes. If I was coding for production -
I would of course use `sort()` though =)
