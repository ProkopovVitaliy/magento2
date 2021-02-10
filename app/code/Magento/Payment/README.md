# Magento_Payment module

The **Magento_Payment** module provides the abstraction level for all payment methods, and all logic that should be used
when adding a new payment method.

This logic includes configuration models, separate models for payment data verification and so on. For
example, `Magento\Payment\Model\Method\AbstractMethod` is an abstract model which should be extended by particular
payment methods.

## Installation

Before disabling or uninstalling this module, note that the following modules depends on this module:

- `Magento_Sales`
- `Magento_Vault`
- `Magento_TestModuleFakePaymentMethod`
- `Magento_CardinalCommerce`

For information about a module installation in Magento 2,
see [Enable or disable modules](https://devdocs.magento.com/guides/v2.4/install-gde/install/cli/install-cli-subcommands-enable.html)
.

## Structure

`Gateway/` - directory contains payment gateway implementations.

`Gateway/Command/` - contains `Payment\Gateway\CommandInterface` implementation.

`Gateway/Config/` - handles configs models.

`Gateway/Data/` - contains DTO and data adapters implementation for payment.

`Gateway/ErrorMapper/` - contains implementation for error message mappers.

`Gateway/Helper/` - handles payment helper models.

`Gateway/Http/` - contains http client implementation classes.

`Gateway/Request/` - contains request builder implementation classes.

`Gateway/Response/` - contains response handler implementation classes.

`Gateway/Validator/` - directory for validators and validator poll implementation.

For information about a typical file structure of a module in Magento 2,
see [Module file structure](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/build/module-file-structure.html#module-file-structure)
.

## Extensibility

We can interact and extends a module using type configuration via `di.xm`.

For example in the `etc\di.xml` we can find:

```xml

<type name="Magento\Payment\Model\Config\Reader">
    <arguments>
        <argument name="fileName" xsi:type="string">payment.xml</argument>
        <argument name="converter" xsi:type="object">Magento\Payment\Model\Config\Converter</argument>
        <argument name="schemaLocator" xsi:type="object">Magento\Payment\Model\Config\SchemaLocator</argument>
    </arguments>
</type>
```

According to example above, we can change all the class configuration. For example if we have to change reader file, we
should to change the file as follows:

```xml

<type name="Magento\Payment\Model\Config\Reader">
    <arguments>
        <argument name="fileName" xsi:type="string">custom_payment.xml</argument>
    </arguments>
</type>
```

As result, we will read config from new `custom_payment.xm` file.

More information
about [type configuration](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/build/di-xml-file.html).

Also, Developer can add new payment carts. For achieve this aim should to extend `etc/payment.xml` config.

For example:

1. create new config file in the custom module by path `etc/payment.xml`.
2. add the next content:

```xml
<?xml version="1.0"?>
<payment xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Payment:etc/payment.xsd">
    <credit_cards>
        <type id="CUSTOM" order="0">
            <label>My Custom Cart</label>
        </type>
    </credit_cards>
</payment>
```

More about [config files](https://devdocs.magento.com/guides/v2.4/config-guide/config/config-create.html).

Extension developers can interact with the Magento_Payment module. For more information about the Magento extension
mechanism, see [Magento plug-ins](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/plugins.html).

### Events

This module observes the following event:

- `sales_order_save_before` in the `Magento\Payment\Observer\SalesOrderBeforeSaveObserver` file.
- `sales_order_status_unassign` in the `Magento\Payment\Observer\UpdateOrderStatusForPaymentMethodsObserver` file.

The module dispatches the following events:

- `payment_form_block_to_html_before` event in the `Magento\Payment\Block\Form\Cc::_toHtml()` method. Parameters:
    - `block` is a block that will be rendered (`Magento\Payment\Block\Form\Cc` class).
- `payment_cart_collect_items_and_amounts` event in the `Magento\Payment\Model\Cart::_collectItemsAndAmounts()` method.
  Parameters:
    - `cart` - is a block that will be rendered (`Magento\Payment\Model\Cart` class).
- `payment_method_assign_data_METHOD_CODE` event in the `Magento\Payment\Model\Method\AbstractMethod::assignData()`
  method. Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\AbstractMethod` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).
- `payment_method_assign_data` event in the `Magento\\Payment\\Model\\Method\\AbstractMethod::assignData()` method.
  Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\AbstractMethod` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).
- `payment_method_is_active` event in the `Magento\Payment\Model\Method\AbstractMethod::isAvailable()` method.
  Parameters:
    - `result` - is a result that will be set (`Magento\Framework\DataObject` class).
    - `method_instance` is a payment method that dispatches current
      event ( `Magento\\Payment\\Model\\Method\\AbstractMethod` class).
    - `quote` is a quote object (`Magento\Quote\Api\Data\CartInterface` interface implementation).
- `payment_method_is_active` event in the `Magento\Payment\Model\Method\Adapter::isAvailable()` method. Parameters:
    - `result` - is a result that will be set (`Magento\Framework\DataObject` class).
    - `method_instance` is a payment method adapter that dispatches current
      event ( `Magento\\Payment\\Model\\Method\\Adapter` class).
    - `quote` is a quote object (`Magento\Quote\Api\Data\CartInterface` interface implementation).
- `payment_method_assign_data_METHOD_ADAPER_CODE` event in the `Magento\Payment\Model\Method\Adapter::assignData()`
  method. Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\Adapter` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).
- `payment_method_assign_data` event in the `Magento\Payment\Model\Method\Adapter::assignData()` method. Parameters:
    - `method` - is a payment method (`Magento\Payment\Model\Method\Adapter` class).
    - `payment_model` - is a payment method info implementation `Magento\Payment\Model\InfoInterface`.
    - `data` - is a data of the payment method that will assign (`Magento\Framework\DataObject` class).

For information about an event in Magento 2,
see [Events and observers](http://devdocs.magento.com/guides/v2.3/extension-dev-guide/events-and-observers.html#events).

### Layouts

The module interacts with the following layout handles:

`view/frontend/layout`:

- `checkout_index_index`
- `checkout_onepage_review`

For more information about a layout in Magento 2, see
the [Layout documentation](https://devdocs.magento.com/guides/v2.4/frontend-dev-guide/layouts/layout-overview.html).

### UI components

Module provides class options for listing column method: `Magento\Payment\Ui\Component\Listing\Column\Method\Options`
which are using in the next ui components:

- `sales_order_creditmemo_grid`
- `sales_order_grid`
- `sales_order_invoice_grid`
- `sales_order_shipment_grid`
- `sales_order_view_creditmemo_grid`
- `sales_order_view_invoice_grid`
- `sales_order_view_shipment_grid`

## Additional information

### ACL

Module introduces the following resources:

- `Magento_Payment::payment` - Payment Methods Section
- `Magento_Payment::payment_services` - Payment Services

More information
about [Access Control List rule](https://devdocs.magento.com/guides/v2.4/ext-best-practices/tutorials/create-access-control-list-rule.html)
.

### Credit cards

Module provides default credit carts types config in the `etc/payment.xml` file.

Types:

- `AE` - American Express
- `VI` - Visa
- `MC` - MasterCard
- `DI` - Discover
- `SM` - Maestro/Switch
- `SO` - Solo
- `JCB` - JCB
- `OT` - Other
- `DN` - Diners
- `MI` - Maestro International
- `MD` - Maestro Domestic
- `HC` - Hipercard
- `ELO` - Elo
- `AU` - Aura

Get all cart types developer can using `Magento\Payment\Model\Config\Reader::read()` method.

For information about significant changes in patch releases, see [Release information](https://devdocs.magento.com/guides/v2.4/release-notes/bk-release-notes.html).
