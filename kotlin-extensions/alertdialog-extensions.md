---
description: >-
  This is custom AlertDialog which can easily invoke from Activity or from
  Fragment.
---

# AlertDialog Extensions

## AlertDialog Kotlin Extension

{% code-tabs %}
{% code-tabs-item title="AlertDialogExtensions.kt" %}
```kotlin
import android.annotation.SuppressLint
import android.app.Activity
import android.content.Context
import android.view.LayoutInflater
import android.view.View
import android.widget.Button
import android.widget.TextView
import androidx.annotation.StringRes
import androidx.appcompat.app.AlertDialog
import androidx.fragment.app.Fragment
import au.com.elegantmedia.presentation.R


/**
 * Created by Chathura Wijesinghe 
 * cdanasiri@gmail.com
 */


inline fun Activity.alert(title: CharSequence? = null, message: CharSequence? = null, func: AlertDialogHelper.() -> Unit): AlertDialog {
    return AlertDialogHelper(this, title, message).apply {
        func()
    }.create()
}

inline fun Activity.alert(titleResource: Int = 0, messageResource: Int = 0, func: AlertDialogHelper.() -> Unit): AlertDialog {
    val title = if (titleResource == 0) null else getString(titleResource)
    val message = if (messageResource == 0) null else getString(messageResource)
    return AlertDialogHelper(this, title, message).apply {
        func()
    }.create()
}

inline fun Fragment.alert(title: CharSequence? = null, message: CharSequence? = null, func: AlertDialogHelper.() -> Unit): AlertDialog {
    return AlertDialogHelper(this.context!!, title, message).apply {
        func()
    }.create()
}

inline fun Fragment.alert(titleResource: Int = 0, messageResource: Int = 0, func: AlertDialogHelper.() -> Unit): AlertDialog {
    val title = if (titleResource == 0) null else getString(titleResource)
    val message = if (messageResource == 0) null else getString(messageResource)
    return AlertDialogHelper(this.context!!, title, message).apply {
        func()
    }.create()
}

@SuppressLint("InflateParams")
class AlertDialogHelper(context: Context, title: CharSequence?, message: CharSequence?) {

    private val dialogView: View by lazyFast {
        LayoutInflater.from(context).inflate(R.layout.dialog_info, null)
    }

    private val builder: AlertDialog.Builder = AlertDialog.Builder(context)
        .setView(dialogView)

    private val title: TextView by lazyFast {
        dialogView.findViewById<TextView>(R.id.dialogInfoTitleTextView)
    }

    private val message: TextView by lazyFast {
        dialogView.findViewById<TextView>(R.id.dialogInfoMessageTextView)
    }

    private val positiveButton: Button by lazyFast {
        dialogView.findViewById<Button>(R.id.dialogInfoPositiveButton)
    }

    private val negativeButton: Button by lazyFast {
        dialogView.findViewById<Button>(R.id.dialogInfoNegativeButton)
    }

    private var dialog: AlertDialog? = null

    var cancelable: Boolean = true

    init {
        this.title.text = title
        this.message.text = message
    }

    fun positiveButton(@StringRes textResource: Int, func: (() -> Unit)? = null) {
        with(positiveButton) {
            text = builder.context.getString(textResource)
            setClickListenerToDialogButton(func)
        }
    }

    fun positiveButton(text: CharSequence, func: (() -> Unit)? = null) {
        with(positiveButton) {
            this.text = text
            setClickListenerToDialogButton(func)
        }
    }

    fun negativeButton(@StringRes textResource: Int, func: (() -> Unit)? = null) {
        with(negativeButton) {
            text = builder.context.getString(textResource)
            setClickListenerToDialogButton(func)
        }
    }

    fun negativeButton(text: CharSequence, func: (() -> Unit)? = null) {
        with(negativeButton) {
            this.text = text
            setClickListenerToDialogButton(func)
        }
    }

    fun onCancel(func: () -> Unit) {
        builder.setOnCancelListener { func() }
    }

    fun create(): AlertDialog {
        title.goneIfTextEmpty()
        message.goneIfTextEmpty()
        positiveButton.goneIfTextEmpty()
        negativeButton.goneIfTextEmpty()

        dialog = builder
            .setCancelable(cancelable)
            .create()
        return dialog!!
    }

    private fun TextView.goneIfTextEmpty() {
        visibility = if (text.isNullOrEmpty()) {
            View.GONE
        } else {
            View.VISIBLE
        }
    }

    private fun Button.setClickListenerToDialogButton(func: (() -> Unit)?) {
        setOnClickListener {
            func?.invoke()
            dialog?.dismiss()
        }
    }
}

/**
 * Implementation of lazy that is not thread safe. Useful when you know what thread you will be
 * executing on and are not worried about synchronization.
 */
fun <T> lazyFast(operation: () -> T): Lazy<T> = lazy(LazyThreadSafetyMode.NONE) {
    operation()
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="dialog\_info.xml" %}
```markup
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:elevation="8dp"
        android:minWidth="300dp"
        android:orientation="vertical"
        android:padding="16dp">

    <TextView
            android:id="@+id/dialogInfoTitleTextView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.5"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"/>

    <TextView
            android:id="@+id/dialogInfoMessageTextView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            app:layout_constraintTop_toBottomOf="@+id/dialogInfoTitleTextView"
            app:layout_constraintEnd_toEndOf="@+id/dialogInfoTitleTextView"
            app:layout_constraintStart_toStartOf="@+id/dialogInfoTitleTextView"/>
    <Button
            android:layout_width="wrap_content"
            android:layout_height="0dp"
            android:id="@+id/dialogInfoPositiveButton"
            app:layout_constraintTop_toBottomOf="@+id/dialogInfoMessageTextView" android:layout_marginTop="64dp"
            app:layout_constraintEnd_toEndOf="parent"/>
    <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:id="@+id/dialogInfoNegativeButton"
            app:layout_constraintEnd_toStartOf="@+id/dialogInfoPositiveButton" android:layout_marginEnd="16dp"
            app:layout_constraintBottom_toBottomOf="@+id/dialogInfoPositiveButton"
            app:layout_constraintTop_toTopOf="@+id/dialogInfoPositiveButton"/>


</androidx.constraintlayout.widget.ConstraintLayout>
```
{% endcode-tabs-item %}
{% endcode-tabs %}



You can simply show AlertDialog using bellow pease of code



```kotlin
alert("Title", "Message: Are you sure") {
      positiveButton("Ok") {
          // Do positive stuff here 
      }
      negativeButton("Cancel"){
          // Do negative stuff here 
      }
  }.show()
```

OR

```kotlin
 alert(R.string.title, R.string.message) {
     positiveButton(R.string.ok) {
         // Do positive stuff here 
     }
     negativeButton(R.string.cancel){
         // Do negative stuff here 
     }
 }.show()
```



