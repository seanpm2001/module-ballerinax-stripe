# module-ballerinax-stripe

This module allows you to access the Stripe API through Ballerina. Stripe is a service that allows users to accept payments online, specifically developers. With the Stripe application, users can keep track of payments, search past payments, create recurring charges, and keep track of customers.

The following sections provide you details on how to use the Stripe connector.

- [Compatibility](#compatibility)
- [Feature Overview](#feature-overview)
- [Getting Started](#getting-started)
- [Samples](#samples)

## Compatibility

|                             |           Version           |
|:---------------------------:|:---------------------------:|
| Ballerina Language          |            1.2.x            |

## Feature Overview

There are 7 collections provided by Ballerina for the moment to interact with different API groups of the Stripe API. 
1. **stripe:Account** - Represents your Stripe account. It holds the credentials to connect with the Stripe account. It also acts as the base object which can be used to retrieve other collection objects.
2. **stripe:Customers** - Represents customers in Stripe. This acts as a client to create, retrieve, update, delete and list customers in Stripe Account.
3. **stripe:Charges** - Represents Charges in Stripe. This acts as a client to create, retrieve, update, capture and list charges in Stripe Account.
4. **stripe:Invoices** - Represents Invoices in Stripe. This acts as a client to create, retrieve, update, delete, finalize, pay, send for mannual payment, void, mark uncollectible and list invoices in Stripe Account
5. **stripe.Plans** - Represents Billing Plans in Stripe. This acts as a client to create, retrieve, update, delete and list plans in Stripe Account.
6. **stripe.Products** - Represents Products in Stripe. This acts as a client to create, retrieve, update, delete and list products in Stripe Account.
7. **stripe.Subscriptions** - Represents Subscriptions in Stripe. This acts as a client to create, retrieve, update, delete and list subscriptions in Stripe Account.

## Getting Started

### Prerequisites
Download and install [Ballerina](https://ballerinalang.org/downloads/).

### Pull the Module
Execute the below command to pull the Stripe module from Ballerina Central:
```ballerina
$ ballerina pull ballerinax/stripe
```

## Samples

### Stripe Sample
The Stripe Client Connector can be used to interact with the Stripe API.

```ballerina
import ballerinax/stripe;
import ballerina/io;

public function main() {

    stripe:Account stripeAccount = new ("<secret-key>");

    // Create a customer.
    stripe:Customer customerParams = {
        address: {
            city: "city1",
            line1: "234/5"
        },
        description: "Test customer",
        email: "abc@gmail.com",
        shipping: {
            address: {
                city: "city1",
                line1: "4523/5"
            },
            name: "John",
            phone: "0924343434"
        },
        name: "John"
    };
    
    stripe:Customers customers = stripeAccount.customers();
    stripe:Customer|stripe:Error response = customers->create(customerParams);
    if (response is stripe:Customer) { 
        anydata customerId =  response["id"];
        if (customerId is string) { 
            io:println("Customer created. Name = " + customerId);
        }
    } else {
        io:println("Error" + response.detail()?.message.toString());
    }

    // Capture the payment of an existing, uncaptured, charge 
    stripe:Charges charges = stripeAccount.charges();
    stripe:Charge|stripe:Error capturedCharge = charges->capture("<charge-id>"); 
    if (capturedCharge is stripe:Charge) {
        anydata chargeId = capturedCharge["id"];
        if (chargeId is string) {
            io:println("Captured charge. Id = " + chargeId);
        }
    } else {
        io:println("Error" + capturedCharge.detail()?.message.toString());
    }

    // Create an invoice.
    stripe:Invoice invoiceParams = {
        customer: "<customer-id>",
        collection_method: "send_invoice",
        description: "Invoice for ABC customer"
    };

    stripe:Invoices invoices = stripeAccount.invoices();
    stripe:Invoice|stripe:Error invoice = invoices->create(invoiceParams);
    if (invoice is stripe:Invoice) {
        anydata invoiceId = invoice["id"];
        if (invoiceId is string) {
            io:println("Invoice created. Id = " + invoiceId);
        }
    } else {
        io:println("Error" + invoice.detail()?.message.toString());
    }

    // Create a product.
    stripe:Product productParams = {
        active: true,
        attributes: ["size", "colour"],
        caption: "Blue Cup",
        description: "This is a blue cup",
        images: ["https://media.gettyimages.com/photos/red-cup-picture-id171368204?s=612x612", "https://i.ytimg.com/vi/3lX0tg7CEJw/maxresdefault.jpg"],
        name: "Blue Cup",
        package_dimensions: {
            height : 3.2,
            weight: 9.8,
            length: 8.7,
            width: 2.3
        },
        shippable: true,
        'type: "good"
    };

    stripe:Products products = stripeAccount.products();
    stripe:Product|error product = products->create(productParams);
    if (product is stripe:Product) {
        anydata productId = product["id"];
        if (productId is string) {
            io:println("Product created. Id = " + productId);
        }
    } else {
        io:println("Error" + product.detail()?.message.toString());
    }

    // Retrieve details of a billing plan.
    stripe:Plans plans = stripeAccount.plans();
    stripe:Plan|error plan = plans->retrieve("<plan_id>");
    if (plan is stripe:Plan) {
        anydata planId = plan["id"];
        if (planId is string) { 
            io:println("Plan information. Id " + planId);
        }
    } else {
        io:println("Error" + plan.detail()?.message.toString());
    }

    // Cancel a subscription.
    stripe:Subscriptions subscriptions = stripeAccount.subscriptions();
    stripe:Subscription|error canceledSubscription = subscriptions->cancel("<subscription id>");
    if (canceledSubscription is stripe:Subscription) {
        anydata subscriptionId = canceledSubscription["id"];
        if (subscriptionId is string) {
            io:println("Canceled subscription. Id " + subscriptionId);
        }
    } else {
        io:println("Error" + canceledSubscription.detail()?.message.toString());
    }
}
```
