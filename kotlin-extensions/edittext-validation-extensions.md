# EditText Validation Extensions

## EditText Validation Extensions

You can validate EditText whether it has a valid email, minimum and maximum length validation etc. 

![Login validation](https://lh3.googleusercontent.com/0aaAHtjlztgFvQG7yil5GWhlUiecG3DTrY01jUUGYgI4aheF-E1leKXDXMzNOfCmTYuZZVJ86CUwo3SvEkKxcZoy-0J4vaV66fuOBPT9y-WmbwaKzbgrCKF_No8_zROGtH27UaI4)

{% code-tabs %}
{% code-tabs-item title="EditTextExtensions.kt" %}
```kotlin
import android.text.Editable
import android.text.TextWatcher
import android.widget.EditText


/**
 * Created by Chathura Wijesinghe on 2019-06-12.
 * cdanasiri@gmail.com
 */

/**
 * This extension supports EditText validation such as emails, maximum and minimum length
 * */
fun EditText.afterTextChanged(afterTextChanged: (String) -> Unit) {
    this.addTextChangedListener(object : TextWatcher {
        override fun afterTextChanged(s: Editable?) {
            afterTextChanged.invoke(s.toString().trim())
        }

        override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {}

        override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {}
    })
}

/**
 * You can validate the EditText like
 *
 * et_email.validate("Valid email address required"){ s -> s.isValidEmail() }
 *
 * et_email.validate("Minimum length is 6"){ s-> s.length>=6 }
 *
 * */
fun EditText.validate(message: String, validator: (String) -> Boolean) : Boolean{
    this.afterTextChanged {
        this.error = if (validator(it)) null else message
    }
    this.error = if (validator(this.getString())) null else message

    return validator(this.getString())
}

/**
 * @return String value of the EditTextView
 * */
fun EditText.getString(): String{
    return this.text.toString()
}

/**
 * @return Trimmed String value of the EditTextView
 * */
fun EditText.getStringTrim(): String{
    return this.getString().trim()
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### To validate Email address 

```kotlin
/**
* Add StringExtention to validate the emails 
*
*	fun String.isValidEmail(): Boolean = this.isNotEmpty() &&
*        Patterns.EMAIL_ADDRESS.matcher(this.trim()).matches()
* 
*/
var isEmail = et_email.validate("Valid email address required"){ s -> s.isValidEmail() }
```

### To check minimum and Maximum length 

```kotlin
et_email.validate("Minimum length is 6"){ s-> s.length>=4 && s.length<=10 }
```

