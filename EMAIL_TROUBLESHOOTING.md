# Email Troubleshooting Guide

## Problem: Emails Not Being Received

If you see `[INFO] MFA email sent successfully` in logs but don't receive emails, here are the most common causes:

### 1. Check Debug Mode (Quick Fix)

Since `MFA_DEBUG_MODE=true` is set in your Koyeb config, the code should be logged and returned in the API response.

**Check the logs:**
- Look for `[DEBUG] MFA CODE for {email}: {code}` in Koyeb logs
- The API response should include `debug_code` field

**Check the API response:**
```json
{
  "message": "MFA code sent to your email",
  "debug_code": "123456"  // This should be present if MFA_DEBUG_MODE=true
}
```

### 2. Brevo Account Issues

**Common Brevo Issues:**

1. **Unverified Sender Email**
   - Go to Brevo dashboard → Senders & IP
   - Verify that `FROM_EMAIL` (islamriden@gmail.com) is verified
   - Unverified senders may show "success" but emails won't be delivered

2. **Sandbox/Test Mode**
   - Free Brevo accounts may be in sandbox mode
   - In sandbox mode, emails only go to verified test recipients
   - Check Brevo dashboard → Settings → Account

3. **Daily Sending Limits**
   - Free tier has limits (usually 300 emails/day)
   - Check if you've exceeded the limit

4. **Email in Spam Folder**
   - Check spam/junk folder
   - Add sender to contacts
   - Check email provider's spam filters

### 3. Verify Brevo Configuration

**Check in Koyeb:**
1. Go to your service → Settings → Secrets
2. Verify `BREVO_API_KEY` is set correctly
3. Verify `FROM_EMAIL` matches your Brevo verified sender

**Test Brevo API directly:**
```bash
curl -X POST "https://api.brevo.com/v3/smtp/email" \
  -H "api-key: YOUR_BREVO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sender": {"email": "islamriden@gmail.com", "name": "FasarliAI"},
    "to": [{"email": "ahmedbenyaflah42@gmail.com"}],
    "subject": "Test Email",
    "htmlContent": "<p>Test email</p>"
  }'
```

### 4. Check Koyeb Logs

After the update, logs will show:
- `[DEBUG] MFA CODE for {email}: {code}` - The actual code
- `[DEBUG] Brevo API Response: {...}` - Full API response
- Any error details if sending fails

**To view logs:**
```bash
koyeb logs your-service-name
```

Or in Koyeb dashboard: Service → Logs

### 5. Common Solutions

**Solution 1: Use Debug Code (Immediate)**
- Check Koyeb logs for `[DEBUG] MFA CODE`
- Or check API response for `debug_code` field
- Use that code to login

**Solution 2: Verify Brevo Sender**
1. Login to Brevo dashboard
2. Go to Senders & IP
3. Verify `islamriden@gmail.com` is verified
4. If not, verify it (check email for verification link)

**Solution 3: Check Email Provider**
- Gmail may block emails from unverified senders
- Check spam folder
- Check email provider's security settings

**Solution 4: Use Different Email Service**
- Consider using SendGrid, Mailgun, or AWS SES
- Or use SMTP with Gmail (requires app password)

### 6. Updated Code Features

The updated code now:
- ✅ Always logs code when `MFA_DEBUG_MODE=true`
- ✅ Logs full Brevo API response for debugging
- ✅ Shows detailed error messages if sending fails
- ✅ Returns `debug_code` in API response when debug mode is enabled

### 7. Quick Test

**Test the login endpoint:**
```bash
curl -X POST https://your-backend.koyeb.app/api/users/login \
  -H "Content-Type: application/json" \
  -d '{"email": "ahmedbenyaflah42@gmail.com", "password": "test"}'
```

**Expected response:**
```json
{
  "message": "MFA code sent to your email",
  "debug_code": "123456"  // Present if MFA_DEBUG_MODE=true
}
```

**Check logs for:**
```
[DEBUG] MFA CODE for ahmedbenyaflah42@gmail.com: 123456
[INFO] MFA email sent successfully to ahmedbenyaflah42@gmail.com
[DEBUG] Brevo API Response: {...}
```

### 8. Alternative: Use Gmail SMTP

If Brevo continues to have issues, you can switch to Gmail SMTP:

1. Generate Gmail App Password
2. Update code to use SMTP instead of Brevo API
3. Use `GOOGLE_EMAIL` and `GOOGLE_APP_PASSWORD` from environment

This is more reliable for personal projects.

---

## Next Steps

1. **Check Koyeb logs** for the debug code
2. **Verify Brevo sender** email is verified
3. **Check spam folder** in recipient's email
4. **Test with debug code** to confirm it works
5. **Fix Brevo configuration** if needed

The debug code should work immediately while you fix the email delivery issue.

