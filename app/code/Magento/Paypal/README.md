# Magento_Paypal module

**Magento_Paypal** module implements integration with the PayPal payment system. Namely, it enables the following
payment methods:

* PayPal Express Checkout
* PayPal Payments Standard
* PayPal Payments Pro
* PayPal Credit
* PayFlow Payment Gateway

## Installation

Before disabling or uninstalling this module, note that the following modules depends on this module:

- `Magento_PaypalCaptcha`
- `Magento_PaypalGraphQl`

During installation module creates following tables in the database:

- `paypal_billing_agreement` - Sales Billing Agreement
- `paypal_billing_agreement_order` - Sales Billing Agreement Order
- `paypal_settlement_report` - Paypal Settlement Report Table
- `paypal_settlement_report_row` - Paypal Settlement Report Row Table
- `paypal_cert` - Paypal Certificate Table
- `paypal_payment_transaction` - PayPal Payflow Link Payment Transaction

During installation module creates following columns in the database tables :

- `quote_payment` - Sales Flat Quote Payment table
    - `paypal_payer_id` - Paypal Payer ID column
    - `paypal_payer_status` - Paypal Payer Status column
    - `paypal_correlation_id` - Paypal Correlation ID column
- `sales_order` - Sales Flat Order table
    - `paypal_ipn_customer_notified` - Paypal Ipn Customer Notified column

For information about a module installation in Magento 2,
see [Enable or disable modules](https://devdocs.magento.com/guides/v2.4/install-gde/install/cli/install-cli-subcommands-enable.html)
.

## Structure

`CustomerData/` - contains section source implementations.

`Gateway/` - directory contains PayPal payment gateway implementations.

`Gateway/Payflowpro/Command/` - contains `Payment\Gateway\CommandInterface` implementation for PayPal flow.

For information about a typical file structure of a module in Magento 2,
see [Module file structure](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/build/module-file-structure.html#module-file-structure)
.

## Extensibility

Developers can add new credit carts types, methods and groups. For achieve this aim should extend `etc/payment.xml`.

For example let's add new credit cart type:

1. In your module create file by path `etc/payment.xml`.
2. Add a new config in the `credit_cards` node:

```xml

<credit_cards>
    <type id="CM" order="0">
        <label>Custom Cart</label>
    </type>
</credit_cards>
```

Extension developers can interact with the Magento_Paypal module. For more information about the Magento extension
mechanism, see [Magento plug-ins](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/plugins.html).

### Events

This module observes the following event:

`adminhtml` area.

- `payment_method_is_active` in the `Magento\Paypal\Observer\RestrictAdminBillingAgreementUsageObserver` file.

`frontend` area.

- `checkout_submit_all_after` in the `Magento\Paypal\Observer\SaveOrderAfterSubmitObserver` file.
- `checkout_controller_onepage_saveOrder` in the `Magento\Paypal\Observer\SetResponseAfterSaveOrderObserver` file.
- `shortcut_buttons_container` in the `Magento\Paypal\Observer\AddPaypalShortcutsObserver` file.

`global` area.

- `sales_order_payment_place_end` in the `Magento\Paypal\Observer\AddBillingAgreementToSessionObserver` file.
- `sales_order_payment_transaction_html_txn_id` in the `Magento\Paypal\Observer\HtmlTransactionIdObserver` file.
- `payment_method_assign_data_payflowpro` in the `Magento\Paypal\Observer\PayflowProAddCcData` file.

The module dispatches the following events:

- `paypal_express_place_order_success` event in
  the `Magento\Paypal\Controller\Express\AbstractExpress\PlaceOrder::execute()` method. Parameters:
    - `order` is an order that will be created (`Magento\Sales\Model\Order` class).
    - `quote` is a last successful quote (`Magento\Quote\Model\Quote` class).
- `paypal_express_place_order_success` event in the `Magento\Paypal\Controller\Express\OnAuthorization::execute()`
  method. Parameters:
    - `order` is an order that will be created (`Magento\Sales\Model\Order` class).
    - `quote` is a last successful quote (`Magento\Quote\Model\Quote` class).
- `paypal_checkout_success` event in the `Magento\Paypal\Controller\Ipn\Index::execute()` method. Parameters:
    - `order` is an order (`Magento\Sales\Model\Order` class).
- `payment_method_assign_data_METHOD_CODE` event in the `Magento\Paypal\Model\Payflowpro::assignData()` method.
  Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\AbstractMethod` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).
- `payment_method_assign_data` event in the `Magento\Paypal\Model\Payflowpro::assignData()` method. Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\AbstractMethod` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).

