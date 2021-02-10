# Magento_PageCache module

The **Magento_PageCache** module provides functionality of caching full pages content in Magento application.

An administrator may switch between built-in caching and Varnish caching. Built-in caching is default and ready to use
without the need of any external tools. Requests and responses are managed by PageCache plugin. It loads data from cache
and returns a response. If data is not present in cache, it passes the request to Magento and waits for the response.
Response is then saved in cache. Blocks can be set as private blocks by setting the property `_isScopePrivate` to `true`
.

These blocks contain personalized information and are not cached in the server. These blocks are being rendered using
AJAX call after the page is loaded. Contents are cached in browser instead. Blocks can also be set as non-cacheable by
setting the 'cacheable' attribute in layout XML files. For
example `<block class="Block\Class" name="blockname" cacheable="false" />`. Pages containing such blocks are not cached.

## Installation details

Before disabling or uninstalling this module, note that the following modules depends on this module:

- `Magento_GraphQlCache`
- `Magento_Persistent`

For information about a module installation in Magento 2,
see [Enable or disable modules](https://devdocs.magento.com/guides/v2.4/install-gde/install/cli/install-cli-subcommands-enable.html)
.

## Structure

`Exception/` - directory contain custom exceptions implementations.

For information about a typical file structure of a module in Magento 2,
see [Module file structure](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/build/module-file-structure.html#module-file-structure)
.

### Events

This module observes the following event:

`etc/` directory:

- `core_layout_render_element` - in the `Magento\PageCache\Observer\ProcessLayoutRenderElement` file.
- `clean_cache_by_tags` - in the `Magento\PageCache\Observer\FlushCacheByTags` file.
- `clean_cache_after_reindex` - in the `Magento\PageCache\Observer\FlushCacheByTags` file.
- `adminhtml_cache_flush_system` - in the `Magento\PageCache\Observer\FlushAllCache` file.
- `adminhtml_cache_flush_all` - in the `Magento\PageCache\Observer\FlushAllCache` file.
- `clean_media_cache_after` - in the `Magento\PageCache\Observer\InvalidateCache` file.
- `clean_catalog_images_cache_after` - in the `Magento\PageCache\Observer\InvalidateCache` file.
- `assigned_theme_changed` - in the `Magento\PageCache\Observer\FlushCacheByTags` file.
- `assign_theme_to_stores_after` - in the `Magento\PageCache\Observer\InvalidateCache` file.
- `controller_action_postdispatch_adminhtml_system_currency_saveRates` - in
  the `Magento\PageCache\Observer\InvalidateCache` file.
- `controller_action_postdispatch_adminhtml_system_config_save` - in the `Magento\PageCache\Observer\InvalidateCache`
  file.
- `controller_action_postdispatch_catalog_product_save` - in the `Magento\PageCache\Observer\FlushCacheByTags` file.
- `controller_action_postdispatch_adminhtml_system_currencysymbol_save` - in
  the `Magento\PageCache\Observer\InvalidateCache` file.
- `model_save_commit_after` - in the `Magento\PageCache\Observer\FlushCacheByTags` file.
- `backend_auth_user_login_success` - in the `Magento\PageCache\Observer\FlushFormKey` file.
- `customer_login` - in the `Magento\PageCache\Observer\FlushFormKey` file.
- `customer_logout` - in the `Magento\PageCache\Observer\FlushFormKey` file.

The module dispatches the following events:

- `adminhtml_cache_refresh_type` event in the `Magento\PageCache\Model\Cache\Type::clean()` method.
- `depersonalize_clear_session` event in
  the `Magento\PageCache\Model\Layout\DepersonalizePlugin::afterGenerateElements()` method.

For information about an event in Magento 2,
see [Events and observers](https://devdocs.magento.com/guides/v2.4/extension-dev-guide/events-and-observers.html#events)
.

### Layout

The module interacts with the following layout handles:

`view/adminhtml/layout/` directory:

- `adminhtml_system_config_edit`

`view/frontend/layout/` directory:

- `default`

For more information about a layout in Magento 2, see
the [Layout documentation](https://devdocs.magento.com/guides/v2.4/frontend-dev-guide/layouts/layout-overview.html).
