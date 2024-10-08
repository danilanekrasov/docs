---
title: How to top up your personal account
description: Follow this guide to top up your personal account.
---

# Top up your personal account

{% include [card-business-nonresidents](../../_includes/billing/card-business-nonresidents.md) %}

{% include [personal-account-balance](../_includes/personal-account-balance.md) %}


The method for topping up your personal account depends on your legal status.


## Individuals {#individuals}

{% include [individual-nonresidents](../../_includes/billing/individual-nonresidents.md) %}

To top up your personal account:

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}

   1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
   1. Select a billing account.
   1. Click **{{ ui-key.yacloud_billing.billing.account.dashboard-overview.button_refill }}**.
   1. In the window that opens, enter the payment amount and click **{{ ui-key.yacloud_billing.billing.account.dashboard-overview.button_refill }}**.
   1. Enter your card details and click **Pay**.

{% endlist %}

{% include [payment-card-types](../../_includes/billing/payment-card-types.md) %}

Your payment will be processed in real time and completed within 15 minutes.

## Businesses and individual entrepreneurs {#legal-entities}


To top up your personal account:

1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
1. Select a billing account.
1. Click **{{ ui-key.yacloud_billing.billing.account.dashboard-overview.button_refill }}**. This button is only available after [switching to paid consumption](activate-commercial.md).
1. Select a payment method:

{% list tabs group=payments %}

- Bank transfer {#transfer}

   Enter the payment amount and click **{{ ui-key.yacloud_billing.billing.account.dashboard-overview.popup-refill_button_company-action }}**.

   The system will generate a payment invoice. Print the invoice and use it to make a payment in a bank or using a banking client system.

   Before paying, please make sure the following is correct in your payment order:
   * Payment amount.
   * Banking information of Yandex.Cloud LLC (Russia), Cloud Services Kazakhstan LLP (Kazakhstan), and Iron Hive doo Beograd (Serbia) for non-residents of Russia and Kazakhstan.
   * Your company or individual entrepreneur TIN.
   * [Personal account number](../concepts/personal-account.md#id) in the payment details.
   * [Contract number](../concepts/contract.md) in the payment details.

   [How fast the funds will be credited to your personal account](../payment/payment-methods-business.md#limits) depends on the bank performing the transaction.

   {% include [payment-bill-note](../_includes/payment-bill-note.md) %}


{% endlist %}
