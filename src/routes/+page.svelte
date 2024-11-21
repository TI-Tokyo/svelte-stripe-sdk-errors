<script lang="ts">
  import { env } from "$env/dynamic/public";
  import {
    loadStripe,
    type Stripe,
    type StripeElementLocale,
    type StripeElements,
    type StripeError,
    type StripePaymentElement,
  } from "@stripe/stripe-js";
  import { onMount } from "svelte";

  const { PUBLIC_STRIPE_API_KEY } = env;

  let stripe: null | Stripe = null;
  let elements: StripeElements;
  let paymentElement: HTMLDivElement;
  let stripeError: StripeError | null = null;
  let stripePaymentElement: StripePaymentElement;
  let processing: boolean = false;

  async function initStripe(locale: StripeElementLocale) {
    return await loadStripe(PUBLIC_STRIPE_API_KEY, {
      locale,
      apiVersion: "2024-10-28.acacia",
    });
  }

  async function initialiseStripeElements(stripeInstance: Stripe) {
    elements = stripeInstance.elements({
      locale: "en",
      mode: "payment",
      amount: 1000,
      currency: "jpy",
      paymentMethodCreation: "manual",
    });

    stripePaymentElement = elements.create("payment", {
      layout: {
        type: "accordion",
        defaultCollapsed: false,
        radios: false,
        spacedAccordionItems: true,
      },
      defaultValues: {
        billingDetails: {
          name: "John Doe",
          email: "example@example.com",
        },
      },
    });

    stripePaymentElement.mount(paymentElement);
  }

  async function submit() {
    if (processing || !stripe) return;

    try {
      await elements.submit();

      // confirm payment with stripe
      const result = await stripe.createPaymentMethod({
        elements,
        params: {
          billing_details: {
            name: "John Doe",
            email: "example@example.com",
            phone: "07012345678",
          },
        },
      });

      // log results, for debugging
      console.log({ result });

      if (result.error) {
        // payment failed, notify user
        stripeError = result.error;
      } else {
        // attach peyment method to customer on server
      }
    } catch (error) {
      console.log(error);
    } finally {
      processing = false;
    }
  }

  onMount(() => {
    try {
      initStripe("en").then((instance) => {
        if (!instance) return;

        stripe = instance;

        initialiseStripeElements(stripe);
      });
    } catch (error) {
      console.log("FAILED TO INITIALISE STRIPE.", error);
    }
  });
</script>

<div class="wrapper">
  <form on:submit|preventDefault={submit}>
    <div bind:this={paymentElement}></div>

    <button disabled={processing}>
      {#if processing}
        Processing...
      {:else}
        Pay
      {/if}
    </button>

    {#if stripeError}
      {JSON.stringify(stripeError, null, 2)}
    {/if}
  </form>
</div>

<style>
  .wrapper {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
