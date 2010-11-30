# Billing Notes

These are for me. And maybe for you.

## What’s happening?

When a user submits their credit card info, it goes to Braintree.

Braintree processes it, and then directs the user back to the site. One way to get around this whole redirection thing is with an iframe, per the comment in the e-mail from Braintree Actual.

When the payment is approved from Braintree, it also creates a subscription for the user at the Monotask site. This subscription's rebill date is in one month from the current date.

## The User Experience

So the flow should be:

   1. User enters (valid) info and hits submit
   2. Data is passed to Braintree. They process it, and approve the user.
   3. On the page that gets loaded (in the background), a new Subscription is created, for the user.
   4. The user gets a confirmation message on the screen that their account is fully set up, and they may now download the application for their computer.

One possible way of making it work: Have a partial, something like /subscriptions/_new.html.erb that notes: "You have now signed up, and your download should start automatically." That page would then be what loads up when the remote submission (AJAX) gives an approval code. TOTALLY NOT SURE HOW TO MAKE THIS WORK.
What About Rebilling?

At Braintree, we can submit re-authorizations from the server (S2S submission). We'll run this with a cron job off the subscriptions model: Subscription.run_monthly_rebilling
Updating a Subscription

The "your account" box has a "billing" module. If the user already has a subscription, it shows one field. If not, it shows a different one. The two fields have different forms. The "already has a subscription" form simply adds the user's billing information at Braintree, but isn't processing a sale / transaction. The "no subscription yet" form will submit the credit card information to Braintree, processes it as a sale, and then adds a subscription to the Subscriptions table for that user.

## Error Cases

### Payment Method Creation
* Our site loads, but Braintree is inaccessible.
* The user enters in an invalid credit card number.
* The user enters in an invalid credit card expiration date.

### Subscription Renewal
* Braintree is inaccessible.
* User's payment method has passed its expiration date.
* User's payment method has been cancelled.