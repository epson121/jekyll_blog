---
layout: post
title: '"repeat your email address" validation in Magento'
date: 2015-09-25 20:11:36.000000000 +00:00
categories:
- javascript
- Magento
tags:
- email
- form validation
- html
- javascript
- Magento
- PHP
- validation
author: luka
---
What can we do to implement a **repeat email** frontend validation?

Imagine a form like this one:
![Form screenshot]({{ "/assets/img/dup_email.png" | absolute_url }})

What can we do to implement a **repeat email** frontend validation? As you already know, Magento has a class-based frontend validation of forms. If you add a class to the form field, it will pass through the validation, and a message will appear if it validation failed. For example, adding `class="required"` to your elements will prevent the form submission until that field is populated. Easy as that. Where are those rules located? `/js/prototype/validation.js` has many rules (eg. `validate-cc-number`, `validate-email`, `validate-number` etc.).

How should we go about implementing our `validate-both-emails` rule? If you've checked the **validation.js** file I've mentioned above, you could see that all the rules are added via `Validation.addAllThese([array of rules])`. So, we should do precisely that in our own javascript file (the one you will include on your page).

Here is the code:

```js
Validation.addAllThese([ ['validate-both-email', 'Please make sure your emails match', function(v, input) {
  var dependentInput = $('first_email_input_id');
  var isEqualValues  = (input.value == dependentInput.value);

  if (input.value == dependentInput.value && dependentInput.hasClassName('validation-failed')) {
    Validation.test(this.className, dependentInput);
  }

  return dependentInput.value == '' || isEqualValues;
}]]);
```

Now, if you add a `class="validate-both-email"` to your repeated email input, and instantiate Magento validation in the same file as the form like this `var myForm = new VarienForm('your_form_id');`, your form (ie. Magento) will check if both fields are entered and if they match before it submits the form.

Cool, right?

Cheers.