# Stockist Product Enquiry Form

Ek simple HTML form jisme stockists product availability, discount aur delivery time bharte hain. Submit karte hi data automatically Google Sheet (dashboard) me save ho jata hai.

## Files
- `index.html` (ya `stockist_form.html`) — Poora form, koi backend/server nahi chahiye.

## Kaise Kaam Karta Hai
1. Form browser me khulta hai.
2. Stockist apna data bharta hai (name, phone, companies, product availability/discount, delivery time, suggestions).
3. "Generate Submission Summary" button dabane par:
   - Summary screen pe dikhti hai (download bhi kar sakte ho).
   - Data Google Apps Script webhook ke through Google Sheet me ek nayi row me save ho jata hai.

## Google Sheet Dashboard Setup (already done once)
1. Google Sheets pe nayi sheet banayi.
2. Extensions → Apps Script me ye code daala:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  if (sheet.getLastRow() === 0) {
    sheet.appendRow(["Timestamp","Name","Phone","Companies","Delivery","Available Count","Total","Available Items","Suggestions"]);
  }

  sheet.appendRow([
    data.timestamp, data.name, data.phone, data.companies,
    data.delivery, data.availableCount, data.totalCount,
    data.availableItems, data.suggestions
  ]);

  return ContentService.createTextOutput("OK");
}
```

3. Deploy → New deployment → Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Deploy → Authorize → Web app URL copy kiya.
5. Wahi URL form ke `GOOGLE_SHEET_WEBHOOK_URL` variable me daala gaya hai (HTML file ke `<script>` section me, `const GOOGLE_SHEET_WEBHOOK_URL = "..."`).

⚠️ Agar kabhi webhook URL change karna ho (naya deployment banaya), to yahi line update karni hogi.

## Dashboard Check Karna
Google Sheet kholo jisme Apps Script lagaya tha — har form submission ki ek nayi row wahan aa jayegi (Timestamp, Name, Phone, Companies, Delivery, Available Count, Available Items, Suggestions).

## Deploy Karna (Vercel)
1. [vercel.com](https://vercel.com) pe account banao.
2. Naya project banao aur ye `index.html` file (form ko `index.html` naam dekar) upload/connect karo.
   - GitHub se karna ho to: naya repo banao, `index.html` push karo, fir Vercel se us repo ko connect karo.
3. Deploy click karo — kuch second me live URL milega (jaise `your-project.vercel.app`).
4. Koi build command/config nahi chahiye — ye plain static HTML hai.

## Important Notes
- Form pura client-side hai; koi server/database khud manage nahi karna.
- Webhook URL public hai — har submission Sheet me jayega bina extra login ke. Spam se bachne ke liye URL sirf trusted stockists ko hi share karein.
- Agar future me naya Apps Script deployment banaoge to URL form me update karna na bhoolein.
