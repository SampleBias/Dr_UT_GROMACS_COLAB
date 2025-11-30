# Google Drive Setup Instructions

## Your Credentials File Location

Your OAuth client secret file is located at:
```
/home/samplebias/Downloads/client_secret_1085724460234-gpnpbduao71m9opc8453d7n6nrm3md97.apps.googleusercontent.com.json
```

## How to Use in Google Colab

### Option 1: Upload the File (Recommended for First Time)

1. **In the notebook cell**, set `auth_method = "json_upload"`
2. **Run the cell**
3. **Click "Choose Files"** when prompted
4. **Navigate to**: `/home/samplebias/Downloads/`
5. **Select**: `client_secret_1085724460234-gpnpbduao71m9opc8453d7n6nrm3md97.apps.googleusercontent.com.json`
6. **Click "Upload"**

The file will be uploaded to Colab and used for authentication.

### Option 2: Copy to Google Drive First

1. **Upload the file to Google Drive** from your local machine
2. **In the notebook**, set `auth_method = "json_path"`
3. **Set the path** to: `{GoogleDrive}/client_secret_1085724460234-gpnpbduao71m9opc8453d7n6nrm3md97.apps.googleusercontent.com.json`
   (Replace `{GoogleDrive}` with `/content/drive/MyDrive` or use the full path)

### Option 3: Use Interactive Authentication (Easiest)

**Note:** For Google Colab, the interactive authentication method is usually the simplest:

1. **Set `auth_method = "interactive"`** (this is the default)
2. **Run the cell**
3. **Click the authorization link** that appears
4. **Select your Google account**
5. **Click "Allow"**
6. **Copy the authorization code**
7. **Paste it in the input box**

This method doesn't require your credentials file and works seamlessly with Colab.

## Important Notes

- **OAuth Client Secret vs Service Account**: Your file is an OAuth client secret file (for user authentication), not a service account key (for automated access)
- **Colab's drive.mount()**: Uses OAuth internally, so interactive authentication is often simpler
- **File Security**: Never commit your credentials file to Git (it's already in `.gitignore`)

## Troubleshooting

**If upload doesn't work:**
- Make sure the file path is correct
- Check that the file exists in your Downloads folder
- Try using interactive authentication instead

**If path doesn't work:**
- Ensure the file is accessible from Colab (either uploaded or in Drive)
- Use the full path starting with `/content/`

