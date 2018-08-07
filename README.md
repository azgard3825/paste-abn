# paste-abn
Easily copy and paste an ABN into multiple text fields

We were required to add two fields, ABN and ACN, to a form of which each number would be entered into a different input box. 

As part of this I decided it would be nice if the user could just copy and paste their ANB/ACN into the first box and have it fill out the entire field instead of having to manually enter each number in each input.

This is how I achieved this.
Firstly I setup each of the inputs as number inputs with the number pattern on them  
```html
<input type="number" pattern="\d+" class="multi">
```

I then required jQuery to hook into the paste event for any of these boxes
```javascript
// grab each of the elements
var $multiBoxes = $('.multi');

// bind the elements paste event
$multiBoxes.on('paste', function(e){
    input_paste($(this), e);
}); 
```

Now we need to create the function to do the paste
```javascript

/**
 * When the users pastes values into the multi field
 * it fills it out
 *
 * @param $this
 * @param e
 */
function input_paste($this, e){

    // grab the values
    var values = e.originalEvent.clipboardData.getData('text/plain');
    
    // clean the data by stripping out anything that is not a number
    values = values.match(/[0-9]/gi);
    
    // stop the paste event from firing normally so we can override its 
    // normal functionallity
    e.preventDefault();

    // if there is no values then bail
    if(!values.length) return;

    // now we have the values we need to loop through each of them and 
    // set the values of each individual input to these values
    var input = $this;
    $.each(values, function(i, v){

        // grab the value and set the input value
        input.val(v);

        // if this is the last input kill the loop
        if(input.is(':last-child')) return false;

        // set the input to be the next element
        input = input.next();

        // focus on the next element so when the loop is finished we will be
        // focused on the last input
        input.focus();
    });

}

```

Now you have a nice way for users to paste into multiple inputs

There is one limitation for this and that is with the default internet browser on android phones. When the paste event fires the clipboardData has no data. 
