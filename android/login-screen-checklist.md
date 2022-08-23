# Login screen checklist

Quick list of issues:

1. [Network](login-screen-checklist.md#network)
2. [Data validation](login-screen-checklist.md#data-validation)
   * Lazy
   * [Runtime](http://goo.gl/y3EXVR)
3. [Edit Text attributes](login-screen-checklist.md#edit-text-attributes)
4. [Handle keyboard done button](login-screen-checklist.md#handle-keyboard-done-button)
5. [Loading indicator](login-screen-checklist.md#loading-indicator)
   * [Dialog](http://goo.gl/7EcaYf)
     * Handle state
     * Handle cancellation
6. [Encrypt credentials](login-screen-checklist.md#encrypt-credentials)
7. [Login Screen is not always MAIN](login-screen-checklist.md#edit-text-attributes)

### Network

Every time user press _Login_ button first you need to check if Network is available.

```java
private void onLoginClicked() {
    if(!isNetworkOn(getBaseContext())) {
        Toast.makeText(getBaseContext(),
                "No network connection", Toast.LENGTH_SHORT).show();
    } else {
        // do login
    }
}

public boolean isNetworkOn(@NotNull Context context) {
    ConnectivityManager connMgr =
            (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo networkInfo = connMgr.getActiveNetworkInfo();

    return (networkInfo != null && networkInfo.isConnected());
}
```

### Data validation

Before making sign in request to server, do simple validation of _login_ and _password_. For example you can check if those values are not empty or if _login_ - is user _email_, you can check if it match [email pattern](http://developer.android.com/reference/android/util/Patterns.html#EMAIL\_ADDRESS). Here two possible solutions available.

**Lazy**

Occurs only when user click on _sign in_ / _login_ button.

```java
private void onLoginClicked() {
    if(!isDataValid()) {
        Toast.makeText(getBaseContext(),
                "Login or password is incorrect", Toast.LENGTH_SHORT).show();
    } else {
        // do login request
    }
}

public boolean isDataValid() {
    boolean isEmailValid = Patterns.EMAIL_ADDRESS.matcher(getEmail()).matches();
    boolean isPasswordValid = !getPassword().isEmpty();
    return isEmailValid && isPasswordValid;
}

public String getPassword() {
    return mEditPassword.getText().toString().trim(); // mEditPassword - EditText
}

public String getEmail() {
    return mEditEmail.getText().toString().trim(); // mEditEmail - EditText
}
```

**Runtime**

Occurs whenever text inside login and password input fields changed. Login button stays disabled until input data is valid.

```java
private EditText mEditEmail;
private EditText mEditPassword;
private Button mBtnLogin;
private boolean isEmailValid;
private boolean isPasswordValid;

private void initView() {
    mEditEmail = (EditText) findViewById(R.id.editEmail);
    mEditPassword = (EditText) findViewById(R.id.editPassword);

    mEditEmail.addTextChangedListener(new TextWatcher() {
        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {

        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            validateEmail(s.toString());
            updateLoginButtonState();
        }

        @Override
        public void afterTextChanged(Editable s) {

        }
    });

    mEditPassword.addTextChangedListener(new TextWatcher() {
        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {

        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            validatePassword(s.toString());
            updateLoginButtonState();
        }

        @Override
        public void afterTextChanged(Editable s) {

        }
    });

    mBtnLogin = (Button) findViewById(R.id.btnLogin);
    mBtnLogin.setEnabled(false); // default state should be disabled
    mBtnLogin.setOnClickListener(this);
}

private void validatePassword(String text) {
    isPasswordValid = !text.isEmpty();
}

private void validateEmail(String text) {
    isEmailValid = Patterns.EMAIL_ADDRESS.matcher(text).matches();
}

private void updateLoginButtonState() {
    if(isEmailValid && isPasswordValid) {
        mBtnLogin.setEnabled(true);
    } else {
        mBtnLogin.setEnabled(false);
    }
}
```

### Edit Text attributes

[android:hint](http://developer.android.com/reference/android/widget/TextView.html#attr\_android:hint) - hint text to display when the text is empty. [android:singleLine](http://developer.android.com/reference/android/widget/TextView.html#attr\_android:singleLine) - constrains the text to a single horizontally scrolling line instead of letting it wrap onto multiple lines. [android:inputType](http://developer.android.com/reference/android/R.attr.html#inputType) - the type of data being placed in a text field, used to help an input method decide how to let the user enter text. Use `textPassword` for password input field and `textEmailAddress` or `textNoSuggestions` for login input field.

Sample without `inputType` attributes.

[![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-1.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-1.png) [![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-2.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-2.png)

Sample with `inputType="textEmailAddress"` and `inputType="textPassword"` attributes.

[![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-3.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-3.png) [![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-4.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-4.png)

**Notice**, here we have two additional buttons `@` and `.com` to make email typing for user a lot easier, after typing `@` symbol autocomplete shows three commonly used email domains: `gmail`, `hotmal`, `yahoo`. Also password field is now hidden and didn't suggest any autocompletion.

### Handle keyboard done button

Default behavior when user press keyboard's `Done` button - is to close keyboard. Good practice is to handle this click and duplicate your login button click logic.

```java
mEditPassword.setOnEditorActionListener(new TextView.OnEditorActionListener() {
    @Override
    public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {

        boolean isValidKey = event != null && event.getKeyCode() == KeyEvent.KEYCODE_ENTER;
        boolean isValidAction = actionId == EditorInfo.IME_ACTION_DONE;

        if (isValidKey || isValidAction) {
            // do login request
        }
        return false;
    }
});
```

### Loading indicator

Display loading dialog, when login request is performed, is commonly used in lot of applications, however in my opinion this is a bad practice. I would rather replace login button with cancel, and disable user input.

[![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-5.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-5.png) [![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-6.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-6.png)

**Dialog**

Since [Activity.showDialog(..)](http://developer.android.com/reference/android/app/Activity.html#showDialog%28int%29) method is now deprecated, we are forced to use [fragment dialogs](http://developer.android.com/reference/android/app/DialogFragment.html), which brings a lot of issues.

[![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-5.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-5.png) [![enter image description here](https://github.com/dmytrodanylyk/dmytrodanylyk/raw/gh-pages/assets/images/articles/login-checklist-7.png)](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/assets/images/articles/login-checklist-7.png)

**Handle fragment state**

Whenever you try to open/close fragment dialog when activity is invisible you will got crash:

`java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState`

This may happen when user press login button, you show dialog, and then user press home button, so request is completed and dialog is dismissed when application is in background.

How to fix this issue?

* Instead of using `dialogFragment.dismiss()` method use `dialogFragment.dismissAllowingStateLoss()`
* Always close dialog in `onPause` method, and restore state in `onResume`

```java
private boolean isDialogVisible;

private void showLoadingDialog() {
    isDialogVisible = true;
    // show dialog
}

private void closeLoadingDialog() {
    isDialogVisible = false;
    // close dialog
}

@Override
protected void onPause() {
    super.onPause();

    closeLoadingDialog(); // prevent IllegalStateException
}

@Override
protected void onResume() {
    super.onResume();

    if(isDialogVisible) {
        showLoadingDialog();
    }
}
```

Great article available [here](http://www.androiddesignpatterns.com/2013/08/fragment-transaction-commit-state-loss.html).

#### **Handle cancellation**

Whenever user press back button whether loading dialog is visible, you should close it and cancel request. Fragment dialog doesn't have dismiss listener, so you need to create your own.

```java
public class LoadingDialogFragment extends DialogFragment {

    public static LoadingDialogFragment newInstance() {
        return new LoadingDialogFragment();
    }

    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {

        ProgressDialog dialog = new ProgressDialog(getActivity());
        dialog.setMessage(getString(R.string.Loading));

        return dialog;
    }
    
    @Override
    public void onDismiss(DialogInterface dialog) {
        super.onDismiss(dialog);
        triggerActivityListener();
    }

    private void triggerActivityListener() {
        if(getActivity() instanceof OnDialogClosedListener) {
            OnDialogClosedListener listener = (OnDialogClosedListener) getActivity();
            listener.onDialogClosed();
        }
    }

    public interface OnDialogClosedListener {
        public void onDialogClosed();
    }

}
```

In your activity implement `OnDialogClosedListener` and cancel request.

```java
public class LoginActivity extends Activity implements
        LoadingDialogFragment.OnDialogClosedListener {

    @Override
    public void onDialogClosed() {
        // cancel request
    }
}
```

### Encrypt credentials

Almost all applications require entering login and password only during first time. Next time user launch application auto-sign in is performed. Does this mean application save your credentials to preferences? - Not necessary.

If you server side made correctly, after success login request it return you _access token_ or _cookie_, which you can use until expiration date. How you use it? Usually adding _access token_ as a header to all further requests.

In case your _poor_ server requires adding credentials to every request, you need to save them to preferences. It is not safe to save them in open form, since anyone with root level access to the device will be able to see them.

There is a great article which describe [how to store credentials safely](http://android-developers.blogspot.com/2013/02/using-cryptography-to-store-credentials.html).

### Login Screen is not always MAIN

When you have a login screen in your application it doesn't mean it should be your _main launcher_ screen.

```xml
<activity android:name=".LoginActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

**Wrong**

* Launch _Login_ activity
* Check if _access token_ is valid
* If _access token_ valid launch _Dashboard_ activity

**Correct**

* Launch _Dashboard_ activity
* Check if _access token_ is valid
* If _access token_ is not valid launch _Login_ activity

If you think about it, user only sign in once and then you save _access token_ or _user credentials_ and do auto-sign in, until access token is expired. So you can check if _access token_ is valid in your main screen, e.g. _Dashboard_, inside `onCreate` method before view is visible.

This will prevent your application from unnecessary launching _Login_ screen, and speed up application launching.

```java
public class DashboardActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // if user is not signed in, finish current activity
        // and launch login screen
        if (!isUserSignedIn()) {
            finish();
            startLoginActivity();
            return;
        }
        setContentView(R.layout.ac_main);
        // do initialization
    }

    // retrieve access token from preferences
    public boolean isUserSignedIn() {
        return PreferencesManager.getInstance().getAccessToken() != null;
    }

    private void startLoginActivity() {
        Intent intent = new Intent(this, LoginActivity.class);
        startActivity(intent);
    }
}
```
