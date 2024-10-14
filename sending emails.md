In Oracle PL/SQL, the `UTL_SMTP` or `UTL_MAIL` packages are used to send emails directly from the database. The `UTL_MAIL` package is generally preferred for simplicity, as it abstracts a lot of the lower-level details required by `UTL_SMTP`.

### Steps to Send an Email Using PL/SQL:

1. **Configure SMTP Settings**: Ensure that the Oracle database has been configured with the appropriate SMTP server information.
2. **Use `UTL_MAIL` or `UTL_SMTP`**: Choose one of these packages to send the email. The `UTL_MAIL` package is easier to use but requires that you install it and configure the database to use it.

### Step 1: Configure the SMTP Server for Oracle

Before sending an email, Oracle must be configured to use an SMTP server. You can configure the SMTP settings by setting the `smtp_out_server` parameter:

```sql
ALTER SYSTEM SET smtp_out_server='smtp.yourmailserver.com' SCOPE=SPFILE;
```

- Replace `smtp.yourmailserver.com` with your actual SMTP server address.
- Restart the Oracle instance for this change to take effect.

### Step 2: Using `UTL_MAIL` to Send Emails

The `UTL_MAIL` package simplifies email sending. However, you may need to install this package if it’s not already available.

#### Installing `UTL_MAIL`

If not installed, you can install `UTL_MAIL` by running the `utlmail.sql` and `prvtmail.plb` scripts, which are located in the `ORACLE_HOME/rdbms/admin` directory.

```bash
-- Connect as SYSDBA
sqlplus / as sysdba

-- Run the scripts
@?/rdbms/admin/utlmail.sql
@?/rdbms/admin/prvtmail.plb
```

Also, set the `UTL_FILE_DIR` or configure directories with appropriate permissions.

#### Grant Necessary Privileges

The user sending the email needs to have execute privileges on the `UTL_MAIL` package:

```sql
GRANT EXECUTE ON UTL_MAIL TO your_user;
```

You also need to set the `smtp_out_server` parameter as shown earlier.

#### Example: Sending an Email Using `UTL_MAIL`

```plsql
BEGIN
    UTL_MAIL.SEND(
        sender    => 'sender@domain.com',
        recipients => 'recipient@domain.com',
        subject   => 'Test Email from Oracle',
        message   => 'This is a test email sent using the UTL_MAIL package in Oracle.'
    );
    
    DBMS_OUTPUT.PUT_LINE('Email sent successfully.');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error sending email: ' || SQLERRM);
END;
/
```

### Parameters of `UTL_MAIL.SEND`:

- **sender**: The email address of the sender.
- **recipients**: The email address(es) of the recipient(s). Multiple recipients can be separated by commas.
- **cc**: (Optional) Carbon copy email address(es).
- **bcc**: (Optional) Blind carbon copy email address(es).
- **subject**: The subject of the email.
- **message**: The body content of the email.
- **mime_type**: (Optional) Defines the format of the message (e.g., `'text/html'` for HTML emails).

### Advanced Example: Sending an Email with Multiple Recipients and Attachments

```plsql
BEGIN
    UTL_MAIL.SEND(
        sender    => 'sender@domain.com',
        recipients => 'recipient1@domain.com, recipient2@domain.com',
        cc        => 'cc_recipient@domain.com',
        bcc       => 'bcc_recipient@domain.com',
        subject   => 'Test Email with Multiple Recipients',
        message   => 'This is a test email sent to multiple recipients.',
        mime_type => 'text/plain'
    );
    
    DBMS_OUTPUT.PUT_LINE('Email sent successfully.');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error sending email: ' || SQLERRM);
END;
/
```

### Step 3: Using `UTL_SMTP` for More Control (Advanced)

The `UTL_SMTP` package provides more control over sending emails, such as handling the email body as MIME format, but it requires a more complex setup.

#### Example: Sending an Email with `UTL_SMTP`

```plsql
DECLARE
    mail_conn   UTL_SMTP.connection;
    sender      VARCHAR2(50) := 'sender@domain.com';
    recipient   VARCHAR2(50) := 'recipient@domain.com';
    mailhost    VARCHAR2(50) := 'smtp.yourmailserver.com';
    port        NUMBER := 25; -- Use the appropriate port for your SMTP server
    msg_body    VARCHAR2(4000);
BEGIN
    -- Open connection to the SMTP server
    mail_conn := UTL_SMTP.OPEN_CONNECTION(mailhost, port);
    
    -- Initiate SMTP conversation
    UTL_SMTP.HELO(mail_conn, mailhost);
    
    -- Set the sender and recipient
    UTL_SMTP.MAIL(mail_conn, sender);
    UTL_SMTP.RCPT(mail_conn, recipient);
    
    -- Compose the email message
    msg_body := 'Subject: Test Email' || UTL_TCP.CRLF ||
                'From: sender@domain.com' || UTL_TCP.CRLF ||
                'To: recipient@domain.com' || UTL_TCP.CRLF ||
                UTL_TCP.CRLF || -- Blank line to separate headers from body
                'This is a test email sent using UTL_SMTP.';
    
    -- Send the message data
    UTL_SMTP.DATA(mail_conn, msg_body);
    
    -- End the SMTP session
    UTL_SMTP.QUIT(mail_conn);
    
    DBMS_OUTPUT.PUT_LINE('Email sent successfully.');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
        UTL_SMTP.QUIT(mail_conn);
END;
/
```

### Key Points of `UTL_SMTP`:

- **`UTL_SMTP.OPEN_CONNECTION(mailhost, port)`**: Opens a connection to the SMTP server.
- **`UTL_SMTP.HELO(mail_conn, mailhost)`**: Initiates the SMTP conversation.
- **`UTL_SMTP.MAIL(mail_conn, sender)`**: Sets the sender's email address.
- **`UTL_SMTP.RCPT(mail_conn, recipient)`**: Sets the recipient's email address.
- **`UTL_SMTP.DATA(mail_conn, msg_body)`**: Sends the email data (subject, headers, body).
- **`UTL_SMTP.QUIT(mail_conn)`**: Closes the SMTP session.

### Conclusion:

- Use **`UTL_MAIL`** for easier and more straightforward email operations in Oracle.
- Use **`UTL_SMTP`** if you need more control over the email contents, headers, or formatting.
  
For both methods, ensure that the Oracle database has access to an SMTP server, and make sure the necessary privileges are granted to the user sending emails.