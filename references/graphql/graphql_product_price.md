# GraphQL: productPrice Query (Summary)

Source: Fynd Storefront GraphQL API - `productPrice`.

## Signature
```
productPrice(
  fulfillmentOptionSlug: String,
  moq: Int,
  pincode: String,
  size: String!,
  slug: String!,
  storeId: Int
): ProductPriceInfo
```

## Required Args
- `slug`: Product slug.
- `size`: Product size/variant.

## Optional Args
- `storeId`, `pincode`, `fulfillmentOptionSlug`, `moq` (minimum order quantity).

## Returns (ProductPriceInfo)
Common fields include:
- `marked`, `effective`, `discount`
- `markedMin`, `markedMax`, `effectiveMin`, `effectiveMax`
- `currencyCode`, `currencySymbol`
- `perUnitMarked`, `perUnitEffective`, `perUnitDiscount`
- `price`, `priceEffective`, `priceMarked`, `buyBox`, `priceMeta`, `discountPercentage`

## Usage Notes
- Typically used after identifying product `slug` and `size` from a `products` or `product` query.
