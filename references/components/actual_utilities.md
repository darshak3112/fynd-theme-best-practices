# Actual Utility Functions Reference

Complete reference of utility functions found in `src/helper/utils.js`.

## SSR Safety

### isRunningOnClient()
Check if code is running on client-side (browser).

```javascript
import { isRunningOnClient } from '../../helper/utils';

if (isRunningOnClient()) {
  // Safe to use window, document, localStorage, etc.
  const width = window.innerWidth;
}
```

**Implementation**: Checks if `typeof window !== "undefined"` and `globalThis === window`.

## Image Optimization

### transformImage(url, key, width)
Transform image URL with resize parameters and DPR support.

```javascript
import { transformImage } from '../../helper/utils';

const optimizedUrl = transformImage(
  imageUrl,
  'original',  // key to replace
  800          // target width
);
```

**Features**:
- Automatically handles device pixel ratio (DPR)
- Replaces `/key/` pattern in URL with `/resize-w:width/`
- Adds DPR parameter to URL
- Safe URL parsing with fallback

## Currency & Number Formatting

### currencyFormat(value, currencySymbol, locale, currencyCode)
Locale-aware currency formatting with international number support.

```javascript
import { currencyFormat } from '../../helper/utils';

// Basic usage
const formatted = currencyFormat(1234.56, '₹', 'en-IN'); 
// Returns: "₹1,23,456"

// With currency code (auto-detects locale)
const formatted = currencyFormat(1234.56, 'AED', null, 'AED');
// Returns: "AED 1,234.56"
```

**Features**:
- Uses `Intl.NumberFormat` for optimal performance
- Supports Indian numbering system (lakhs/crores)
- Handles alphabetic currency codes (USD, AED) with space
- Symbol currencies (₹, $) without space

### priceFormatCurrencySymbol(symbol, price, locale, currencyCode)
Format price with currency symbol and decimal handling.

```javascript
import { priceFormatCurrencySymbol } from '../../helper/utils';

const price = priceFormatCurrencySymbol('₹', 1999.99, 'en-IN');
// Returns: "₹2,000" (rounded to 2 decimals)
```

### numberWithCommas(number)
Format number with Indian numbering system.

```javascript
import { numberWithCommas } from '../../helper/utils';

const formatted = numberWithCommas(123456);
// Returns: "1,23,456"
```

### roundToDecimals(number, decimalPlaces = 2)
Round number to specific decimal places.

```javascript
import { roundToDecimals } from '../../helper/utils';

const rounded = roundToDecimals(3.14159, 2);
// Returns: 3.14
```

## Date & Time Utilities

### convertDate(dateString, locale = 'en-US')
Convert date string to formatted date with browser timezone.

```javascript
import { convertDate } from '../../helper/utils';

const formatted = convertDate('2024-01-15T10:30:00Z', 'en-US');
// Returns: "January 15, 2024, 10:30 AM"
```

### convertUTCDateToLocalDate(date, format, locale = 'en-US')
Convert UTC date to local timezone with custom format.

```javascript
import { convertUTCDateToLocalDate } from '../../helper/utils';

const formatted = convertUTCDateToLocalDate(
  '2024-01-15T10:30:00Z',
  {
    weekday: 'long',
    month: 'long',
    day: 'numeric',
    year: 'numeric'
  },
  'en-US'
);
// Returns: "Monday, January 15, 2024"
```

##Validation Functions

### validateEmailField(value)
Validate email address format.

```javascript
import { validateEmailField } from '../../helper/utils';

if (!validateEmailField(email)) {
  // Show error
}
```

### validatePhone(phoneNo)
Validate 10-digit phone number.

```javascript
import { validatePhone } from '../../helper/utils';

if (!validatePhone('9876543210')) {
  // Valid
}
```

### validatePasswordField(value)
Validate password strength (min 8 chars, letter + number + special char).

```javascript
import { validatePasswordField } from '../../helper/utils';

if (!validatePasswordField(password)) {
  // Show password requirements
}
```

### validateName(name)
Validate name with allowed characters (letters, numbers, spaces, -_'.).

```javascript
import { validateName } from '../../helper/utils';

if (!validateName(name)) {
  // Invalid characters
}
```

### isValidPincode(value)
Validate pinco de/ZIP code (2-11 alphanumeric characters).

```javascript
import { isValidPincode } from '../../helper/utils';

if (!isValidPincode(pincode)) {
  // Invalid pincode
}
```

### checkIfNumber(value)
Check if string contains only numbers.

```javascript
import { checkIfNumber } from '../../helper/utils';

if (checkIfNumber('12345')) {
  // Only numbers
}
```

## Performance Utilities

### debounce(func, wait)
Debounce function calls.

```javascript
import { debounce } from '../../helper/utils';

const debouncedSearch = debounce((query) => {
  performSearch(query);
}, 300);

// Use in input handler
onChange={(e) => debouncedSearch(e.target.value)}
```

### throttle(func, wait)
Throttle function calls.

```javascript
import { throttle } from '../../helper/utils';

const throttledScroll = throttle(() => {
  handleScroll();
}, 100);

window.addEventListener('scroll', throttledScroll);
```

## GraphQL Utilities

### updateGraphQueryWithValue(mainString, replacements)
Update GraphQL queries with dynamic values.

```javascript
import { updateGraphQueryWithValue } from '../../helper/utils';

const query = updateGraphQueryWithValue(
  originalQuery,
  [
    ['$PRODUCT_ID', productId],
    ['$USER_ID', userId]
  ]
);
```

## User Data Helpers

### getUserFullName(user)
Extract full name from user object.

```javascript
import { getUserFullName } from '../../helper/utils';

const fullName = getUserFullName(user);
// Returns: "John Doe"
```

### getUserPrimaryPhone(user)
Extract primary phone number.

```javascript
import { getUserPrimaryPhone } from '../../helper/utils';

const phone = getUserPrimaryPhone(user);
// Returns: { mobile: "9876543210", countryCode: "91" }
```

### getUserPrimaryEmail(user)
Extract primary email.

```javascript
import { getUserPrimaryEmail } from '../../helper/utils';

const email = getUserPrimaryEmail(user);
// Returns: "user@example.com"
```

### getUserAutofillData(user, isGuestUser = false)
Get autofill data for forms.

```javascript
import { getUserAutofillData } from '../../helper/utils';

const autofillData = getUserAutofillData(user, false);
// Returns: { name, phone, email }
```

## Address Helpers

### getAddressStr(item, isAddressTypeAvailable)
Format address object as display string.

```javascript
import { getAddressStr } from '../../helper/utils';

const addressStr = getAddressStr(address, true);
// Returns: "Home, 123 Main St, Downtown, New York, NY 10001, USA"
```

### getAddressFromComponents(components, name)
Parse Google Maps address components into address object.

```javascript
import { getAddressFromComponents } from '../../helper/utils';

const address = getAddressFromComponents(googleComponents, 'Home');
// Returns: { address, area, landmark, city, state, area_code, country, country_iso_code }
```

## Cookie Utilities

### getCookie(key)
Get cookie value by key.

```javascript
import { getCookie } from '../../helper/utils';

const userData = getCookie('user_data');
```

**Features**:
- Automatically parses JSON values
- Returns null if cookie doesn't exist
- SSR safe (returns null on server)

### removeCookie(name)
Remove cookie by name.

```javascript
import { removeCookie } from '../../helper/utils';

removeCookie('session_token');
```

## Other Utilities

### deepEqual(obj1, obj2)
Deep comparison of two objects.

```javascript
import { deepEqual } from '../../helper/utils';

if (deepEqual(prevState, nextState)) {
  // Objects are identical
}
```

### isEmptyOrNull(obj)
Check if object is empty, null, or undefined.

```javascript
import { isEmptyOrNull } from '../../helper/utils';

if (isEmptyOrNull(data)) {
  // Handle empty case
}
```

### translateDynamicLabel(input, t)
Translate dynamic labels using translation function.

```javascript
import { translateDynamicLabel } from '../../helper/utils';

const translated = translateDynamicLabel('Order Status', t);
```

### translateValidationMessages(validationObject, t)
Translate validation messages in validation object.

```javascript
import { translateValidationMessages } from '../../helper/utils';

const translated = translateValidationMessages(validationRules, t);
```

### getLocaleDirection(fpi)
Get text direction from FPI store.

```javascript
import { getLocaleDirection } from '../../helper/utils';

const direction = getLocaleDirection(fpi);
// Returns: 'ltr' or 'rtl'
```

### replaceQueryPlaceholders(queryFormat, value1, value2)
Replace {} placeholders in filter query strings.

```javascript
import { replaceQueryPlaceholders } from '../../helper/utils';

const query = replaceQueryPlaceholders('price:{}:{}', 100, 500);
// Returns: "price:100:500"
```

### detectMobileWidth()
Detect if device screen width is ≤ 768px.

```javascript
import { detectMobileWidth } from '../../helper/utils';

if (detectMobileWidth()) {
  // Mobile device
}
```

### getProductImgAspectRatio(global_config, defaultAspectRatio = 0.8)
Get product image aspect ratio from global config.

```javascript
import { getProductImgAspectRatio } from '../../helper/utils';

const aspectRatio = getProductImgAspectRatio(globalConfig);
// Returns: 0.8 (or configured value between 0.6 and 1)
```

### injectScript(script)
Dynamically inject script tag and return promise.

```javascript
import { injectScript } from '../../helper/utils';

try {
  await injectScript('https://cdn.example.com/sdk.js');
  // Script loaded successfully
} catch (error) {
  // Script failed to load
}
```

### isNumberKey(e)
Check if keyboard event is a number key.

```javascript
import { isNumberKey } from '../../helper/utils';

onKeyDown={(e) => {
  if (!isNumberKey(e)) {
    e.preventDefault();
  }
}}
```

### isFreeNavigation(e)
Check if keyboard event is a navigation key (delete, backspace, arrows).

```javascript
import { isFreeNavigation } from '../../helper/utils';

onKeyDown={(e) => {
  if (isFreeNavigation(e) || isNumberKey(e)) {
    // Allow
  } else {
    e.preventDefault();
  }
}}
```

### formatLocale(locale, countryCode, isCurrencyLocale = false)
Format locale string with country code validation.

```javascript
import { formatLocale } from '../../helper/utils';

const formattedLocale = formatLocale('en', 'IN', true);
// Returns: 'en-IN'
```

### getConfigFromProps(props)
Extract configuration from props object.

```javascript
import { getConfigFromProps } from '../../helper/utils';

const config = getConfigFromProps(props);
```

### getReviewRatingData(customMeta)
Extract review rating data from custom metadata.

```javascript
import { getReviewRatingData } from '../../helper/utils';

const ratingData = getReviewRatingData(customMeta);
// Returns: { rating_sum, rating_count, avg_ratings }
```
