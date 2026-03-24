# Cart Components — FDK React Templates

Source: `src/pages/cart/cart.jsx`, `src/page-layouts/cart/Components/`

## Architecture Overview

```
src/pages/cart/cart.jsx                        ← Page entry, composes all cart sub-components
src/page-layouts/cart/Components/
  chip-item/          ← Individual cart line item (product card with controls)
  coupon/             ← Coupon apply / remove / list modal
  delivery-location/  ← Pincode / address selection
  gst-card/           ← GST number input
  comment/            ← Order note / comment input
  share-cart/         ← Share cart via QR / social
  share-cart-modal/   ← Modal wrapper for share cart
  sticky-footer/      ← Mobile-sticky checkout bar (login/checkout CTA)
  remove-cart-item/   ← Remove item confirmation modal with wishlist option
  free-gift-item/     ← Free gift item display
```

---

## 1. Cart Page (`src/pages/cart/cart.jsx`)

Orchestrates the full cart UI. Accepts spread prop objects for each sub-component.

### Key Props

| Prop | Purpose |
|------|---------|
| `isLoggedIn` | Shows login/checkout CTAs differently |
| `isAnonymous` | Enables "Continue as Guest" button |
| `cartItems` | Object keyed by `{uid}_{size}` |
| `cartItemsWithActualIndex` | Array of items with API index |
| `breakUpValues` | Price breakdown (`display` array) |
| `isValid` | Checkout button enabled |
| `isOutOfStock` | Disables checkout |
| `isNotServicable` | Disables checkout |
| `currencySymbol` | Currency symbol string |
| `deliveryLocationProps` | Spread into `<DeliveryLocation>` |
| `cartCouponProps` | Spread into `<Coupon>` |
| `cartGstProps` | Spread into `<GstCard>` |
| `cartCommentProps` | Spread into `<Comment>` |
| `cartShareProps` | Spread into `<ShareCart>` |
| `isGstInput` | Show/hide GST card |
| `isShareCart` | Show/hide share cart |

### Layout Structure

```
cartMainContainer
  ├── Error banner (cartData.message)
  └── cartWrapper
       ├── cartItemDetailsContainer (left column)
       │    ├── DeliveryLocation
       │    ├── Bag title + item count
       │    ├── ShareCart (tablet view)
       │    └── ChipItem × N (one per cart item)
       └── cartItemPriceSummaryDetails (right column)
            ├── Coupon
            ├── Comment
            ├── GstCard (if isGstInput)
            ├── PriceBreakup
            ├── Login or Checkout button
            └── ShareCart (desktop view)
StickyFooter (always rendered, mobile-fixed)
RemoveCartItem (modal, portal)
```

### Cart Item Key Format

```js
// Items keyed as "{product_uid}_{size}"
const cartItemsArray = Object.keys(cartItems || {});
// e.g. "10160451_8", "9876543_M"

const currentSize = singleItem?.split("_")[1]; // extract size from key
```

### Authentication Gate

```jsx
// Unauthenticated users see Login + optional Guest Checkout
{!isLoggedIn ? (
  <>
    <button onClick={() => navigate("/auth/login")}>LOGIN</button>
    {isAnonymous && <button onClick={onGotoCheckout}>CONTINUE AS GUEST</button>}
  </>
) : (
  <button disabled={!isValid || isOutOfStock || isNotServicable} onClick={onGotoCheckout}>
    PLACE ORDER
  </button>
)}
```

---

## 2. ChipItem (`src/page-layouts/cart/Components/chip-item/`)

The core cart line item component. Renders product card with all controls.

### Features
- Product image, brand, name, size, quantity display
- Quantity increment/decrement controls
- Size change via modal
- Promotional offers display + promo detail modal
- Remove icon (triggers `RemoveCartItem` modal via parent)
- Stock / serviceability error states
- `isCartUpdating` disables all controls during API call

### Key Props

| Prop | Purpose |
|------|---------|
| `singleItemDetails` | Full cart item object from API |
| `productImage` | Pre-extracted image URL (with resize transform) |
| `currentSize` | Extracted from item key (`key.split("_")[1]`) |
| `isCartUpdating` | Disables quantity/size controls |
| `onUpdateCartItems` | `(newDetails) => void` — called on qty/size change |
| `onRemoveIconClick` | `(data) => void` — parent opens remove modal |
| `sizeModal` / `setSizeModal` | Shared state for which item's size modal is open |
| `cartItemsWithActualIndex` | Needed to find item's API index for updates |
| `isPromoModalOpen` | Whether promo detail modal is open |

### Image URL Pattern

```js
// Parent pre-transforms image URL before passing
const productImage =
  singleItemDetails?.product?.images?.[0]?.url?.replace(
    "original",
    "resize-w:250"
  );
```

### Item Key Structure (`singleItemDetails`)

```js
{
  key: "10160451_8",
  quantity: 2,
  discount: "2% OFF",
  availability: {
    out_of_stock: false,
    deliverable: true,
    is_valid: true,
    sizes: ["8", "9", "10"],
    available_sizes: [{ display: "8", is_available: true, value: "8" }]
  },
  product: { uid, name, brand, images, slug },
  article: { size, seller, price },
  price_per_unit: { converted: { effective, marked, currency_symbol } },
  promotions_applied: [],
  coupon: { code, discount_single_quantity }
}
```

---

## 3. Coupon (`src/page-layouts/cart/Components/coupon/`)

Full coupon management: display applied coupon, open coupon list modal, apply/remove.

### Sub-components (all in same file)
- `Coupon` — main exported component (coupon box + modals)
- `OfferCard` — individual coupon card in the list modal
- `CouponItem` — alternate coupon row style (unused in main flow)
- `CouponSuccessModal` — center modal on successful coupon apply
- `NoCouponsAvailable` — empty state for coupon list

### Key Props

| Prop | Purpose |
|------|---------|
| `hasCancel` | Coupon is applied — show remove (×) instead of arrow |
| `couponCode` | Applied coupon code |
| `couponId` | Applied coupon ID (needed for removal) |
| `couponValue` | Discount amount to display "You saved ₹X" |
| `availableCouponList` | Array of available coupons for the modal list |
| `isCouponListModalOpen` | Controls the coupon list drawer (right-modal) |
| `isCouponSuccessModalOpen` | Controls the success center modal |
| `onCouponBoxClick` | Open the coupon list modal |
| `onApplyCouponClick` | `(couponCode) => void` |
| `onRemoveCouponClick` | `(couponId) => void` |
| `error` | `{ message }` — shown as form error |
| `successCoupon` | Applied coupon data for success modal |

### Coupon Apply Form

Uses `react-hook-form`. Prevents re-submitting same invalid coupon via `lastSubmittedCoupon` state check. Clears error on input change via `watch` subscription.

```js
// Error cleared when user types a new code
watch((value, { name }) => {
  if (name === "couponInput" && errors?.root) {
    clearErrors("root");
  }
});
```

### HTML Coupon Messages

Coupon `message` field may contain HTML. Detected and rendered via `FyHTMLRenderer`:

```js
const hasHTMLTags = useMemo(() => /<[^>]+>/.test(message), [message]);
```

### Modal Types Used
- Coupon list: `modalType="right-modal"` (slides in from right)
- Coupon success: `modalType="center-modal"` (center overlay)

---

## 4. DeliveryLocation (`src/page-layouts/cart/Components/delivery-location/`)

Pincode and delivery address selector. Shows current delivery location and allows user to change it.

### Features
- Displays current pincode / delivery city
- Modal to enter/change pincode
- Modal to select from saved addresses (logged-in users)
- Add new address flow

### Key Props
- `pincode` — current pincode string
- `deliveryAddress` — current selected address object
- `isPincodeModalOpen` / `isAddressModalOpen` — modal states
- `onPincodeSubmit` — `(pincode) => void`
- `onAddressSelect` — `(address) => void`
- `onAddNewAddress` — open add address form

---

## 5. RemoveCartItem (`src/page-layouts/cart/Components/remove-cart-item/`)

Confirmation modal before removing a cart item. Offers option to move to wishlist instead.

### Props

| Prop | Purpose |
|------|---------|
| `isOpen` | Modal visibility |
| `cartItem` | Item data to display (name, image) in confirmation |
| `isRemoving` | Loading state on remove button |
| `onRemoveButtonClick` | Confirm removal |
| `onWishlistButtonClick` | Move to wishlist instead |
| `onCloseDialogClick` | Cancel and close |

### Parent Pattern

```js
// cart.jsx manages state and passes down
function handleRemoveIconClick(data) {
  setRemoveItemData(data);   // store the item to remove
  onRemoveIconClick();       // open the modal
}
// Later:
<RemoveCartItem
  cartItem={removeItemData?.item}
  onRemoveButtonClick={() => onRemoveButtonClick(removeItemData)}
  onWishlistButtonClick={() => onWishlistButtonClick(removeItemData)}
/>
```

---

## 6. StickyFooter (`src/page-layouts/cart/Components/sticky-footer/`)

Mobile-sticky checkout bar. Always rendered. Handles both logged-in and guest states.

### Behavior

| State | Shows |
|-------|-------|
| Not logged in | Total price + "VIEW BILL" + LOGIN button + (optional) CONTINUE AS GUEST |
| Logged in | Total price + "VIEW PRICE DETAILS" + CHECKOUT button |

### Key Props
- `isLoggedIn` — switches the entire footer layout
- `isAnonymous` — shows "Continue as Guest" for not-logged-in
- `isValid` / `isOutOfStock` / `isNotServicable` — disable checkout button
- `totalPrice` — formatted via `currencyFormat(numberWithCommas(totalPrice), ...)`
- `onLoginClick` — navigates to `/auth/login`
- `onCheckoutClick` — proceeds to checkout
- `onPriceDetailsClick` — opens price breakup sheet/modal

### RTL Support

```js
// Rotates arrow icon for RTL layouts
import { isRunningOnClient } from "../../../../helper/utils";

className={
  isRunningOnClient() && document.dir === "rtl" ? styles.rotate180 : ""
}
```

---

## 7. GstCard (`src/page-layouts/cart/Components/gst-card/`)

Optional GST number input for business users.

### Features
- Checkbox to toggle GST input visibility
- GSTIN text input with format validation
- Error display for invalid GSTIN
- Submits GSTIN to apply business pricing

### Controlled by
- `isGstInput` prop on Cart page (from theme config)

---

## 8. Comment (`src/page-layouts/cart/Components/comment/`)

Order note / special instructions input.

### Features
- Shows truncated note with "edit" trigger
- Opens a modal for full text input
- Character limit display
- Saves comment via `onCommentSubmit(text)`

---

## 9. ShareCart (`src/page-layouts/cart/Components/share-cart/`)

Allows users to share their cart with others.

### Features
- Generates shareable cart link
- QR code display
- Social media share buttons

### Rendering Locations
- Tablet: inside `cartItemDetailsContainer` (top)
- Desktop: inside `cartItemPriceSummaryDetails` (bottom, with `showCard={true}`)

```jsx
{isShareCart && (
  <div className={styles.shareCartTablet}>
    <ShareCart {...cartShareProps} />
  </div>
)}
// ...
{isShareCart && (
  <div className={styles.shareCartDesktop}>
    <ShareCart showCard={true} {...cartShareProps} />
  </div>
)}
```

---

## 10. PriceBreakup (`src/components/price-breakup/`)

Shared component (not cart-specific). Renders the line items from `breakUpValues.display`.

```jsx
<PriceBreakup
  breakUpValues={breakUpValues?.display || []}
  cartItemCount={cartItemsArray?.length || 0}
  currencySymbol={currencySymbol}
/>
```

Display items from API: `[{ key: "mrp", display: "MRP", value: 75000, currency_code: "INR" }]`
Total key is `"total"`: `breakUpValues?.display?.find((val) => val.key == "total")?.value`

---

## Translation Keys (Cart)

```
resource.section.cart.your_bag
resource.section.cart.checkout_button
resource.section.cart.checkout_button_caps
resource.section.cart.continue_as_guest
resource.section.cart.continue_as_guest_caps
resource.section.cart.order_on_behalf
resource.cart.apply_coupons
resource.cart.apply_coupon
resource.cart.remove_coupon
resource.cart.coupons_title
resource.cart.view_all_offers
resource.cart.you_have_saved
resource.cart.savings_with_this_coupon
resource.cart.no_coupons_available
resource.cart.coupon_code_prompt
resource.cart.enter_coupon_code
resource.cart.open_coupon_drawer
resource.cart.total_price
resource.cart.view_bill
resource.cart.view_price_details
resource.cart.redeem_rewards_points_worth
resource.common.items
resource.auth.login.login_caps
```

---

## Common Gotchas

1. **Cart items are keyed by `{uid}_{size}`** — always split by `_` to get size. Never assume item key is purely numeric.
2. **`isCartUpdating` must disable all ChipItem controls** — otherwise users can trigger multiple concurrent API calls.
3. **`PriceBreakup` needs `breakUpValues?.display` not the root `breakUpValues`** — guard with `?.length > 0` before rendering the right column.
4. **`RemoveCartItem` receives pre-packaged `removeItemData`** — cart page stores this in state when remove icon is clicked, then passes `removeItemData.item` as `cartItem` prop.
5. **`Coupon` error is set via `react-hook-form`'s `setError("root", error)`** — clear it with `clearErrors("root")`, not by nulling the prop.
6. **`StickyFooter` uses `isRunningOnClient()` for RTL arrow** — safe pattern for SSR.
7. **`ShareCart` renders in two places** — tablet (in item list) and desktop (in price summary). Both receive the same `cartShareProps` spread.
8. **`GstCard` is conditionally rendered** via `isGstInput` prop which comes from theme settings config — don't hardcode it.
9. **`currencyFormat(numberWithCommas(value), symbol, locale)`** — always use this triple pattern for price display, not raw number strings.
