# Bulk EU VAT validation with VIES — practical guide

Check a list of European VAT numbers and get a structured report for your B2B customer records.

**[Open EU VAT Validator on Apify](https://apify.com/clarno/eu-vat-validator-vies)** · [Guida in italiano](#guida-in-italiano)

This guide is maintained by the publisher of `clarno/eu-vat-validator-vies`. It contains usage examples, not the Actor's source code. It is not an official European Commission product.

## What you can do

- Paste VAT numbers, one per line, or provide a CSV file URL.
- Distinguish confirmed valid and invalid numbers from checks VIES could not complete.
- Retrieve company name and address when the national register supplies them.
- Keep your own record reference in structured input to match results to customer records.

## Quick start: paste a list

1. Open the [Actor page](https://apify.com/clarno/eu-vat-validator-vies) and sign in to Apify.
2. In the input form, use **VAT numbers (one per line)**. Include the country prefix. Start with a small list you are authorized to process.
3. Review the displayed price and set a maximum charge limit before starting a run. `maxItems` limits input rows; it is not a monetary budget.
4. Start the run, then open its results dataset. Export the results in the format you need using Apify's dataset controls.

Example JSON input (replace with your own authorized records):

```json
{
  "inputText": "IT00743110157\nDE811569869\nNL810462783B01",
  "maxItems": 3
}
```

These numbers are format examples from the Actor's sample input. Their current validity is not asserted. This guide does not execute checks; running the Actor is subject to its pricing.

## Alternative: CSV URL

```json
{
  "inputText": "",
  "inputCsvUrl": "https://example.com/your-vat-list.csv",
  "csvColumn": "vat_number",
  "maxItems": 10
}
```

Replace the placeholder URL with a CSV URL accessible to the Actor. This input uses a URL, not a direct file-upload control. Clear the prefilled text list when using only a CSV. Do not publish confidential customer files just to make them accessible; paste an authorized list or use structured input instead.

## Alternative: preserve a customer reference

```json
{
  "inputText": "",
  "vatNumbers": [
    {
      "countryCode": "IT",
      "vatNumber": "00743110157",
      "reference": "customer-001"
    }
  ],
  "maxItems": 1
}
```

## Read the result correctly

| Status | Meaning | Charged event? |
| --- | --- | --- |
| `VALID` | VIES confirmed the number as valid at check time | Yes, for a unique completed check in a paid run |
| `INVALID` | VIES returned a negative validity result | Yes, for a unique completed check in a paid run |
| `INVALID_FORMAT` | The configured local validation rejected the format | No |
| `UNVERIFIED` | A definitive VIES response was unavailable | No |
| `REJECTED` | The request was rejected; inspect the error fields | No |
| `NOT_PROCESSED` | The row was not processed, for example because the spending limit was reached | No |

Repeated copies of the same VAT number within a run do not generate additional paid events. Retries are not extra billable checks. An unavailable response is not evidence that a VAT number is invalid.

The output includes `countryCode`, `vatNumber`, `status`, `valid`, `name`, `address`, `reference`, `checkedAt` and error details where relevant. Company fields may be empty. A consultation number is returned only when supplied by VIES; the requester fields can be configured in the input form.

Illustrative outcomes — not live VIES results:

| Record | Status | Next step |
| --- | --- | --- |
| Customer A | VALID | Retain the result and timestamp |
| Customer B | INVALID | Review the supplied number |
| Customer C | UNVERIFIED | Inspect the error and consider a later retry |

## Pricing

As of 12 October 2026: **$5 per 1,000 unique completed checks ($0.005 each)**, with platform usage included in the Actor price. Only unique numbers with a definitive VIES `VALID` or `INVALID` answer generate the paid event. Check the [current pricing](https://apify.com/clarno/eu-vat-validator-vies) before running.

Publishing and reading this guide is free. Running the Actor is a separate operation. Account-specific credits and any separate post-run storage operations follow Apify's terms; see [Apify's pricing-model documentation](https://docs.apify.com/actors/running/actors-in-store).

## Troubleshooting

**No company name or address?** National registers do not always provide these fields.

**VIES is unavailable?** Inspect `errorCode` and `errorMessage`. Temporary failures can be retried by the Actor. Do not interpret `UNVERIFIED` as `INVALID`.

**Unexpected sample numbers in the output?** Clear `inputText` when switching to CSV or structured input.

**Need automation?** The Actor's API tab provides integration examples. Keep API tokens out of public repositories, screenshots and URLs you share.

**Need help?** Use the Actor's Issues tab. Share a redacted example and the error message, not credentials or a customer database.

## Guida in italiano

### Verificare un elenco di partite IVA europee

1. Apri [EU VAT Validator su Apify](https://apify.com/clarno/eu-vat-validator-vies) e accedi.
2. Incolla le partite IVA con prefisso del paese, una per riga, nel campo **VAT numbers (one per line)**.
3. Controlla il prezzo e imposta il limite massimo di spesa. Inizia con pochi record che sei autorizzato a trattare.
4. Avvia il controllo e apri i risultati del run. Puoi esportarli dai controlli del dataset Apify.

In alternativa puoi indicare l'URL di un CSV nel campo **CSV file URL**, specificando la colonna in **CSV column name**. Svuota prima l'elenco di esempio. Non rendere pubblici file riservati dei clienti: puoi usare l'elenco incollato o l'input strutturato.

- **VALID:** VIES conferma la validità al momento della verifica.
- **INVALID:** VIES restituisce un esito negativo.
- **UNVERIFIED:** il controllo non ha ottenuto una risposta definitiva; non significa “partita IVA non valida”.

Nome e indirizzo compaiono solo quando forniti dal registro. Il prezzo indicato al 12 ottobre 2026 è **5 USD ogni 1.000 verifiche uniche completate**, con piattaforma inclusa nel prezzo dell'Actor. Duplicati nello stesso run, formati rifiutati localmente e risposte indisponibili non generano eventi a pagamento. Verifica sempre il prezzo nella pagina dell'Actor prima di avviare.

Questa guida è gratuita; l'esecuzione dell'Actor segue le condizioni mostrate da Apify. Gli esempi non attestano la validità attuale dei numeri riportati. La verifica VIES è un elemento di controllo e non sostituisce le valutazioni fiscali applicabili alla tua attività.
