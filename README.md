📘 Engage 360 - API Integration Guide (v1.0)
This document outlines the technical steps required to integrate Engage 360 into your website for tracking user behavior and events like login, checkout, product views, and orders.

🧩 Step 1: Embed Engage 360 Script
Place the following script inside the <head> tag of every page on your website.

<script>
(function(w, i, g, z, o) {
    var a, m;
    w['WigzoObject'] = o;
    w[o] = w[o] || function() {
        (w[o].q = w[o].q || []).push(arguments);
    };
    w[o].l = 1 * new Date();
    w[o].h = z;
    a = i.createElement(g);
    m = i.getElementsByTagName(g)[0];
    a.async = 1;
    a.src = z;
    m.parentNode.insertBefore(a, m);
})(window, document, 'script', '//app.wigzo.com/wigzo.compressed.js', 'wigzo');

wigzo('configure', 'YOUR_ORG_TOKEN'); // Replace with your actual org_token
</script>
🔑 Step 2: Identify Users (Login & Checkout Pages)
Endpoint:
POST http://tracker.wigzopush.com/rest/v1/learn/identify?token=YOUR_TOKEN&org_token=YOUR_ORG_TOKEN

Sample Payload
json
Copy
Edit
{
  "userId": "a1b2c3d4-e5f6-7890-gh12-ijklmnopqrst",
  "phone": "+919876543210",
  "email": "test@wigzo.com",
  "createdAt": "2022-10-09",
  "updatedAt": "2022-10-09",
  "firstName": "Dev",
  "lastName": "Kaushik",
  "customer_id": "917255095087",
  "postal_code": "1008009",
  "city": "Gurgaon",
  "state": "Haryana",
  "country": "India",
  "country_code": "IN"
}

Parameter Requirements:

Field	Type	Required	Description
userId	string	✅ Yes	Must be a valid UUIDv4. Unique per user.
phone	string	✅ Yes*	Required if email is not provided.
email	string	✅ Yes*	Required if phone is not provided.
firstName	string	Optional	User's first name.
lastName	string	Optional	User's last name.
customer_id	string	Optional	CRM/customer ID from your system.
is_active	boolean	Optional	Defaults to true.
source	string	Optional	Can be web or app.
createdAt	string	Optional	ISO date format.
updatedAt	string	Optional	ISO date format.

⚠️ Either email or phone is mandatory.

🧾 Step 3: Track Events:

Endpoint:
POST http://tracker.wigzopush.com/rest/v1/learn/event?token=YOUR_TOKEN&org_token=YOUR_ORG_TOKEN

Sample Payload
json
Copy
Edit
{
  "eventName": "email_fetched",
  "eventval": {
    "email": "sample@email.com",
    "orderId": "12345"
  },
  "eventCategory": "EXTERNAL",
  "userId": "a1b2c3d4-e5f6-7890-gh12-ijklmnopqrst",
  "is_active": true,
  "source": "web"
}

Parameter Reference:

Field	Type	Required	Description
eventName	string	✅ Yes	Name of the event (e.g., email_fetched, product_view).
eventval	object	✅ Yes	Key-value data for event context.
eventCategory	string	✅ Yes	Always pass "EXTERNAL".
userId	string	✅ Yes	Must be the same UUIDv4 used in Identify API.
is_active	boolean	Optional	Defaults to true.
source	string	Optional	web or app.

📦 Event-Specific Payloads:

1. Product View (On product page)
eventName: "product_view"

{
  "canonicalUrl": "/product/undefined/1694/",
  "tags": "apple, macbook, computer",
  "title": "Power Curve Chimney 90cm",
  "description": "14.5 inches Apple Macbook Pro",
  "price": "72000",
  "productType": "cooker",
  "vendor": "dev",
  "author": "Apple",
  "createdAt": "2022-09-01",
  "updatedAt": "2022-09-01",
  "productId": "11",
  "image": "https://www.youproductimageurl.com",
  "category": "Electronics",
  "language": "en",
  "variants": [
    {
      "variant_id": "12",
      "product_id": "11",
      "title": "test",
      "sku": "test2",
      "image": "testwigzo.jpg",
      "inventory_quantity": "1",
      "price": "72000"
    }
  ]
}
✅ Notes:

productId, variant_id must not be null.

canonicalUrl is required and should be SEO-friendly URL of the product.

2. Order Completed (On Thank You page):
eventName: "order_completed"
{
  "orderId": "3328591",
  "customer_id": "12345",
  "phone": "+919472749140",
  "fullName": "abc xyz",
  "email": "test@wigzo.com",
  "total_price": 3897,
  "total_line_items_price": 3897,
  "cart_token": "uuid-token",
  "checkout_token": "uuid-token",
  "ga_transaction_id": "332859",
  "created_at": "2022-09-23T11:32:20Z",
  "updated_at": "2022-09-23T11:32:20Z",
  "shipping_cost": 0,
  "total_discounts": 3000,
  "city": "New Delhi",
  "state": "Delhi",
  "country": "India",
  "zip": "110030",
  "financial_status": "COD",
  "taxes_included": true,
  "coupons": ["EOSSALE"],
  "line_items": [
    {
      "line_item_id": "123",
      "variant_id": "123",
      "product_id": "123",
      "price": 1999,
      "quantity": 1,
      "product_discount": 0,
      "fulfillment_status": "Pending",
      "categories": "Organisers",
      "type": "planner"
    }
  ]
}
✅ Important:

orderId, customer_id, variant_id, product_id, quantity are mandatory.

total_line_items_price is used for revenue calculations.

3. Checkout Started (On cart page or payment start)
eventName: "checkout_started"
{
  "completed_at": "2019-05-23T18:40:34+05:30",
  "closed_at": "2019-05-23T18:40:34+05:30",
  "referring_site": "https://google.com",
  "currency": "INR",
  "source_identifier": "utm_source=test",
  "total_discounts": "500",
  "total_line_items_price": "1500",
  "total_price": "1000",
  "total_tax": "0",
  "abandoned_checkout_url": "https://yourshop.com/checkout/123",
  "line_items": [
    {
      "checkout_id": "123591",
      "product_id": "45",
      "quantity": 1,
      "title": "Jib",
      "variant_id": "45",
      "variant_title": "Green",
      "variant_price": "1000",
      "vendor": "Ottawa Sail Shop",
      "price": "1000",
      "taxable": true
    }
  ]
}
✅ Note: checkout_id must not be null.

4. Add to Cart (On button click)
Call the event using the Wigzo JS library:
eventName: "add_to_cart"
wigzo("track", "addtocart", "CANONICAL_URL_OF_PRODUCT");
Replace CANONICAL_URL_OF_PRODUCT with the actual URL path, e.g., "/product/macbook-pro".

🔐 Security Note
Keep your token and org_token confidential.

Do not expose them publicly in client-side code wherever possible.

Use HTTPS for all integrations.

📞 Support
If you face any issues or need help integrating, please contact your Wigzo Integration Manager or support team.

