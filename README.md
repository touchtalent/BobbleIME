# BobbleIME SDK

## Overview
BobbleIME sdk provides complete solution to Input Method Editor implementation. BobbleIME provides
following core IME features :

1. Word Suggestion
2. Word Prediction
3. Gesture typing
4. Auto correct
5. Customizable language dictionary
6. Emoji support

This SDK exposes API endpoints to allow certain level of customization of IME.

## How to create custom IME

Import **bobbleime_sdk.aar** as submodule to your android project. Create a custom class which extends 
BobbleIME, this class will act as the input method service.

BobbleIME exposes two place holders which can contain customized view's and functionality, which are
1. Top Bar
2. Custom View

Below is the list of all UI configuration that is possible as part of customization.

Configuration | Description
------------- | -------------
![SDK](docs/1.png "BobbleIME SDK") | IME is opened on a input view that does not supports word suggestion
![SDK](docs/2.png "BobbleIME SDK") | IME is opened on a input view that supports word suggestion
![SDK](docs/3.png "BobbleIME SDK") | IME is opened on a input view that does not supports word suggestion and custom top bar is made visible
![SDK](docs/4.png "BobbleIME SDK") | IME is opened on a input view that supports word suggestion and custom top bar is made visible
![SDK](docs/5.png "BobbleIME SDK") | IME is opened on a input view and both top bar and custom view is visible
![SDK](docs/6.png "BobbleIME SDK") | IME is opened on a custom input view which does not supports word suggestion and both top bar and custom view is visible
![SDK](docs/7.png "BobbleIME SDK") | IME is opened on a custom input view which supports word suggestion and both top bar and custom view is visible


You also need to register your custom IME class in manifest as InputMethod service.

**Example :**

```xml
<service
    android:name=".CustomIME"
    android:label="MyCustomIME"
    android:permission="android.permission.BIND_INPUT_METHOD">
    <intent-filter>
        <action android:name="android.view.InputMethod" />
    </intent-filter>
</service>
```

## API REF

BobbleIME provides following API endpoints:

### 1. API setLicenceKey()

This allows SDK developer to use customization API in their host app. If you don't have a
Licence Key for your host app, you need to request one. Please note that Licence Key do not
superimpose any expiration date, but each Licence Key is bounded to host app package name.

```java
/**
 * Set LicenceKey for the IME SDK.
 *
 * @param licenceKey LicenceKey string
 */
void setLicenceKey(@Nonnull String licenceKey)
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    private String LICENCE_KEY = "...";
    
    @Override
    public void onCreate() {
        // Set Licence Key before making any SDK customization API calls.
        setLicenceKey(LICENCE_KEY);
        
        super.onCreate();
    }
}
```

### 2. API onStartIME()

The custom class that extends BobbleIME, need to override onStartIME(). onStartIME() is called
when the IME is triggered by system and its visible to user. This is the starting point for IME
customization and all customization API calls must start from here.

```java
/**
 * This method will get called when Input Method is visible to the user.
 */
abstract void onStartIME()
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        
        // Add Customization API calls here
        
        super.onStartIME();
    }
}
```

### 3. API onFinishIME()

The custom class that extends BobbleIME, may override onFinishIME(). onFinishIME() is called when
IME service is triggered by system and its about to hide itself.

```java
/**
 * This method will get called when Input Method is hidden from the user.
 */
abstract void onFinishIME()
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    @Override
    public void onFinishIME() {
        
        // Add custom cleanup calls on IME close (if needed)
        
        super.onFinishIME();
    }
}
```

### 4. API setTopBar(View)

The custom class that extends BobbleIME can call setTopBar(View) API to show custom View in
IME's top bar place holder.

```java
/**
 * This method will add the passed view on top of the keyboard view.
 *
 * @param topBarView view that the client wants to add on top of the Input method's Keyboard View.
 * @throws Exception if invalid licence key used for SDK
 */
void setTopBar(@Nonnull View topBarView) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import android.view.View;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
    }
}
```

### 5. API showCustomView(View)

The custom class that extends BobbleIME can call showCustomView(View) API to show a custom view
in the custom view container. When showCustomView(View) is called, the keyboard input view is
closed and the custom view is shown. If already a custom view is visible, it is replaced by the
newly provided custom view.

```java
/**
 * This method will add the passed custom view on top of the keyboard view.
 *
 * @param customView view that the client wants to add on top of the Input Method's Keyboard View
 *                   and below the top bar view that was previously added by the client.
 * @throws Exception if invalid licence key used for SDK
 */
void showCustomView(@Nonnull View customView) throws Exception
```

###### NOTE
> All text input targets in custom view must extend BobbleEditText so keyboard input can interact with it.

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import android.view.View;
import android.widget.Button;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Prepare the custom view
                final View customView = prepareCustomView();
                
                // Call API to show the custom view and hide the keyboard input view
                setCustomView(customView);
            }
        });
    }
    
    private View prepareCustomView() {
        // Place code here to generate custom view that need to be shown in the custom view container
    }
}
```

### 6. API showKeyboardView()

The custom class that extends BobbleIME can call showKeyboardView() API to show keyboard input view
in case it was hidden due to previous call to showCustomView(View) and custom view was visible.
Calling showKeyboardView() will close visible custom view (if any) and will show keyboard input view.

```java
/**
 * This method removes added custom view from Input Method and show Keyboard View.
 *
 * @throws Exception if invalid licence key used for SDK
 */
void showKeyboardView() throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import android.view.View;
import android.widget.Button;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Prepare the custom view
                final View customView = prepareCustomView();
                
                // Call API to show the custom view and hide the keyboard input view
                setCustomView(customView);
            }
        });
        
        // Example of a button in top bar that's going to close custom view and show keyboard input view
        Button topBarButton2 = ...;
        topBarButton2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Call API to show keyboard input view and hide the custom view
                showKeyboardView();
            }
        });
    }
    
    private View prepareCustomView() {
        // Place code here to generate custom view that need to be shown in the custom view container
    }
}
```

### 7. API setInputTarget()

The custom class that extends BobbleIME can call setInputTarget() API to set input method
interaction target programmatically. The input target that is passed as argument to this API **must
be an instance of BobbleEditText** and its visible in any custom view container (top bar or main custom view) previously.

```java
/**
 * This method can be used to change the input target for the Input Method.
 *
 * @param editText BobbleEditText instance
 * @throws Exception if invalid licence key used for SDK
 */
void setInputTarget(@Nonnull EditText editText) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import android.view.View;
import android.widget.Button;
import android.util.Pair;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Prepare the custom view
                final Pair<View, BobbleEditText> customView = prepareCustomView();
                
                // Call API to show the custom view and hide the keyboard input view
                setCustomView(customView.first);
                
                // Call API to open keyboard input view which is attached to the edit text inside
                // custom view
                setInputTarget(customView.second);
            }
        });
    }
    
    private Pair<View, BobbleEditText> prepareCustomView() {
        // Place code here to generate custom view that need to be shown in the custom view container
        
        // Create a edit text and add inside the custom view
        BobbleEditText editText1 = ...;
    }
}
```

### 8. API restoreInputTarget()

The custom class that extends BobbleIME can call restoreInputTarget() API to restore input transaction
back to host app. If a custom view was visible previously and also keyboard input view was visible,
then only custom view is shown and keyboard input view is closed. After this API is called, any input
interaction will be performed on the host app until and unless user does not manually focuses
to a text field inside custom view.

```java
/**
 * This method will restore the connection to the Editor outside the Input Method (ex- Editor in
 * WhatsApp chat screen).
 *
 * @throws Exception if invalid licence key used for SDK
 */
void restoreInputTarget() throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import android.view.View;
import android.widget.Button;
import android.util.Pair;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Prepare the custom view
                final Pair<View, BobbleEditText> customView = prepareCustomView();
                
                // Call API to show the custom view and hide the keyboard input view
                setCustomView(customView.first);
                
                // Call API to open keyboard input view which is attached to the edit text inside
                // custom view
                setInputTarget(customView.second);
            }
        });
        
        // Example of a button in top bar that closes the custom view and attaches the input transaction
        // back to host app
        Button topBarButton2 = ...;
        topBarButton2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // Call API to restore the input transaction back to host app 
                restoreInputTarget();
                
                // Call API to close the custom view
                showKeyboardView();
            }
        });
    }
    
    private Pair<View, BobbleEditText> prepareCustomView() {
        // Place code here to generate custom view that need to be shown in the custom view container
        
        // Create a edit text and add inside the custom view
        BobbleEditText editText1 = ...;
    }
}
```

### 9. API sendTextToConnectedInput()

This API allows you to sent text to current active input connection of the keyboard.

```java
/**
 * Set text to attached input connection if any.
 *
 * @param text text to be set
 * @throws Exception if invalid licence key used for SDK
 */
void sendTextToConnectedInput(@Nonnull String text) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    @Override
    protected void onStartIME() {
        try {
            sendTextToConnectedInput("Hello مرحبا from DEMO");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 10. API addCustomizationListener(BobbleCustomizationListener) and removeCustomizationListener(BobbleCustomizationListener)

This api allows you to register and unregister a BobbleCustomizationListener which reflects
the api calls to showCustomView() and showTopBarView();

```java
/**
 * Set a listener to receive keyboard customization callbacks.
 *
 * @param bobbleCustomizationListener Listener instance for receiving customization events.
 * @throws Exception if invalid licence key used for SDK
 */
void addCustomizationListener(@Nonnull BobbleCustomizationListener bobbleCustomizationListener) throws Exception
```

```java
/**
 * Un set a listener if its already registered to receive customization calls.
 *
 * @param bobbleCustomizationListener Listener instance for receiving customization events that was registered before.
 * @throws Exception if invalid licence key used for SDK
 */
void removeCustomizationListener(@Nonnull BobbleCustomizationListener bobbleCustomizationListener) throws Exception 
```

```java
/**
 * Class can provide implementation to this class to listen CustomView and TopBarView add
 * event.
 */
public interface BobbleCustomizationListener {
    /**
     * This callback is fired when a custom view is added.
     *
     * @param customView The view that has been added as customization call.
     */
    void onCustomViewAdded(View customView);

    /**
     * This callback is fired when a custom top bar view is added.
     *
     * @param topBarView The view that has been added as top bar customization call.
     */
    void onTopBarViewAdded(View topBarView);
}
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleCustomizationListener;

class CustomIME extends BobbleIME implements BobbleCustomizationListener {
    @Override
    protected void onStartIME() {
        // Add a listener for customization
        try {
            addCustomizationListener(this);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    @Override
    protected void onFinishIME() {
        // Remove registered listener for customization
        try {
            removeCustomizationListener(this);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    @Override
    public void onCustomViewAdded(View customView) {
        // Perform some action
    }

    @Override
    public void onTopBarViewAdded(View topBarView) {
        // Perform some action
    }
}
```

### 11. API setIMESettings() and getIMESettings()

This api allows you to change keyboard settings and get current settings values for keyboard.

```java
/**
 * Customize keyboard settings.
 *
 * @param settings Settings to be customized.
 * @param isEnable Boolean true if this settings needs to be enabled, false otherwise.
 * @throws Exception if invalid licence key used for SDK
 */
void setIMESettings(@Nonnull BobbleIMESettings settings, boolean isEnable) throws Exception
```

```java
/**
 * Get current keyboard settings.
 *
 * @param settings Settings for which state need to be obtained.
 * @return Boolean true if the settings is enabled, false otherwise.
 * @throws Exception if invalid licence key used for SDK
 */
boolean getIMESettings(@Nonnull BobbleIMESettings settings) throws Exception
```

```java
/**
 * List of all supported keyboard settings for customization.
 */
public enum BobbleIMESettings {
    AUTO_CAPITALIZATION,
    AUTO_CORRECTION,
    KEY_VIBRATION,
    KEY_PRESS_SOUND,
    KEY_POPUP,
    WORD_SUGGESTION,
    NEXT_WORD_SUGGESTION,
    PERSONALIZE_SUGGESTION,
    GESTURE_TYPING,
    DOUBLE_SPACE_PERIOD,
    VOICE_INPUT,
    BLOCK_OFFENSIVE_WORD
}
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    @Override
    protected void onStartIME() {
        try {
            // Get current settings for key popup
            boolean isKeyPopupShown = getIMESettings(BobbleIMESettings.KEY_POPUP);
            
            // Set key popup disabled
            setIMESettings(BobbleIMESettings.KEY_POPUP, false);
        }
        catch (Exception e) {
            e.printStackTrace();;   
        }
    }
}
```


### 12. API setIMESettingsTitle()

This api allows you to change keyboard settings title.

```java
/**
 * Customize keyboard settings title.
 *
 * @param title Setting title to be customized.
 * @throws Exception if invalid licence key used for SDK
 */
public final void setIMESettingsTitle(@Nonnull String title) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;

class CustomIME extends BobbleIME {
    @Override
    protected void onStartIME() {
        try {
            setIMESettingsTitle(title);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 13. API shareContent()

This method will be used to share to the open app (or optionally a specified destination app) which has triggered the keyboard. 
This method will send across the content passed to it to the destination app.

```java
/**
* Share to the open app (or optionally a specified destination app) which has triggered the keyboard.
* This method will send across the content passed to it to the destination app.
*
* @param contentUri contentUri of the shared content
* @param mimeType Mime Type of the shared content
* @param packageName Package name of the application where content has to be shared
* @throws Exception if invalid licence key used for SDK
*/
public final void shareContent(@NonNull Uri contentUri, @NonNull String mimeType,
                               @NonNull String packageName) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                File file = new File(path); // path to the file that has to be shared
                Uri contentUri = FileProvider.getUriForFile(this, Constants.AUTHORITY, file);
                shareContent(contentUri, mimeType.toString(), getCurrentInputEditorInfo().packageName);
            }
        });
    }
}
```

### 14. API showOverlay()

This method will add the passed overlay view on top of the keyboard view.

```java
/**
* This method will add the passed overlay view on top of the keyboard view.
*
* @param overlayView view that the client wants to add on top of the Input Method's Keyboard View
*                   and below the top bar view that was previously added by the client.
*
* @throws Exception if invalid licence key used for SDK
*/
public final void showOverlayView(@NonNull View overlayView) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Create overlay view
                View view = prepareOverlayView();
                
                //Call showOverlayView
                showOverlayView(view);
            }
        });
    }
    
    private View prepareOverlayView() {
        // Place code here to generate overlay view that need to be shown on keyboard view.
    }
}
```

### 15. API hideOverlayView()

This will remove the currently visible overlay from the keyboard.

```java
/**
* This method hides added overlay view from Input Method and show Keyboard View.
*
* @throws Exception if invalid licence key used for SDK
*/
public final void hideOverlayView() throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Hide overlay view
                hideOverlayView(view);
            }
        });
    }
}
```

### 16. API loadSuggestionView()

This method will add the passed custom view in suggestion bar.

```java
/**
* This method will add the passed custom view in suggestion bar.
*
* @param suggestionView view that the client wants to add on top of suggestion bar.     
*
* @throws Exception if invalid licence key used for SDK
*/
public final void loadSuggestionView(@NonNull View suggestionView) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Create custom suggestion view
                View view = prepareCustomSuggestionView();
                
                //Load suggestion view 
                loadSuggestionView(view);
            }
        });
    }
    
    private View prepareCustomSuggestionView() {
        // Place code here to generate custom suggestion view that need to be added in suggestion bar.
    }
}
```

### 17. API showTopBar() and hideTopBar()

These APIs allow to toggle visibility of top bar.

```java
/**
* This method will show the top bar that has already been added.                 
*
* @throws Exception if invalid licence key used for SDK
*/
public final void showTopBar() throws Exception

/**
* This method will hide the top bar that has already been added.
* 
* @throws Exception if invalid licence key used for SDK
*/
public final void hideTopBar() throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        Button topBarButton2 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Show top bar 
                showTopBar();
            }
        });
        topBarButton2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Hide top bar 
                hideTopBar();
            }
        });
    }
}
```

### 18. API loadTheme() and loadDefaultTheme()

These APIs allow to set the appearance of keyboard. The theme passed should be a valid one and should have valid color values (hex strings).

```java
/**
* This API is used to set the theme of keyboard.
*
* @param theme BobbleIMETheme object holding theme specific values.
* @throws Exception if invalid licence key used for SDK or theme is invalid
*/
public final void loadTheme(@NonNull BobbleIMETheme theme) throws Exception

/**
* This API is used to reset the theme to default, i.e. the light theme.
* 
* @throws Exception if invalid licence key used for SDK
*/
public final void loadDefaultTheme() throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        Button topBarButton2 = ...;
        
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Set Blue Theme 
               setBlueTheme();
            }
        });
        
        topBarButton2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
               //Set Default Theme 
               loadDefaultTheme();
            }
        });
    }
    
    private void setBlueTheme() {
        BobbleIMETheme bobbleIMETheme = new BobbleIMETheme();
        
        bobbleIMETheme.setThemeId(4);
        bobbleIMETheme.setThemeName("Blue");
        
        bobbleIMETheme.setIsLightTheme(false);
        
        bobbleIMETheme.setKeyboardBackgroundColor("#1565C0");
        
        bobbleIMETheme.setKeyBackgroundColor("#4D000000");
        bobbleIMETheme.setKeyTextColor("#FFFFFF");
        bobbleIMETheme.setFunctionalTextColor("#FFFFFF");
        
        bobbleIMETheme.setKeyHintLetterColor("#FFFFFF");
        bobbleIMETheme.setKeyHintLabelColor("#FFFFFF");
        
        bobbleIMETheme.setKeyPreviewBackgroundColor("#0D47A1");
        bobbleIMETheme.setKeyPreviewTextColor("#FFFFFF");
        
        bobbleIMETheme.setSuggestionsBarBackgroundColor("#0D47A1");
        bobbleIMETheme.setSuggestionsColorValidTypedWord("#D9FFFFFF");
        bobbleIMETheme.setSuggestionsColorTypedWord("#D9FFFFFF");
        bobbleIMETheme.setSuggestionsColorAutoCorrect("#FFFFFF");
        bobbleIMETheme.setSuggestionsColorSuggested("#B3FFFFFF");
        
        bobbleIMETheme.setMoreSuggestionsPanelBackgroundColor("#2196F3");
        bobbleIMETheme.setMoreSuggestionsButtonBackgroundColor("#64B5F6");
        
        bobbleIMETheme.setSwipeGestureTrailColor("#42A5F5");
        bobbleIMETheme.setGestureFloatingPreviewTextColor("#FFFFFF");
        bobbleIMETheme.setGestureFloatingPreviewColor("#42A5F5");

        bobbleIMETheme.setEnterKeyCircleBackgroudColor("#2979FF");
                            
        bobbleIMETheme.setShowTopKeys(false);
        bobbleIMETheme.setShowKeyBorder(true);
        
        loadTheme(bobbleIMETheme);
    }
}
```
For custom themes, you need to set following values :

```java
bobbleIMETheme.setBackgroundImagePath(<absolute path to image>)
```
OR
```java
bobbleIMETheme.setBackgroundDrawable(<resource id of drawable>)
```

### 19. API setKeyboardLanguage

This API is used to set current keyboard language.

```java
/**
* This API is used to set current keyboard language.                 
* 
* @param languageType which language to be set. Should be ENGLISH or ARABIC.
* @throws Exception if invalid licence key used for SDK or languageType is invalid
*/
public final void setKeyboardLanguage(LanguageType languageType) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME {
    @Override
    public void onStartIME() {
        // Build custom view that need to be shown in the tob bar container
        final View customTopBarView = prepareTopBar();
        
        // Attach the view to the top bar container
        setTopBar(customTopBarView);
        
        super.onStartIME();
    }
    
    private View prepareTopBar() {
        // Place code here to generate custom view that need to be shown in the top bar
        
        // Example of a button in top bar that's going to show custom view on click
        Button topBarButton1 = ...;
        Button topBarButton2 = ...;
        topBarButton1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Show English Keyboard 
                setKeyboardLanguage(LanguageType.ENGLISH);
            }
        });
        topBarButton2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Show Arabic Keyboard
                setKeyboardLanguage(LanguageType.ARABIC);
            }
        });
    }
}
```

### 20. API addWordCommitListener() and removeWordCommitListener()

These APIs allow to register and unregister listeners for word commit events.

```java
/**
* Set a listener to receive keyboard word commits.                 
*
* @param wordCommitListener Listener instance for receiving word commit events.
* @throws Exception if invalid licence key used for SDK
*/
public final void addWordCommitListener(@Nonnull BobbleWordCommitListener wordCommitListener) throws Exception
```

```java
/**
* Remove a listener if its already registered to receive customization calls.
* 
* @param wordCommitListener Listener instance for receiving word commit events that was registered before.
* @throws Exception if invalid licence key used for SDK
*/
public final void removeWordCommitListener(@Nonnull BobbleWordCommitListener wordCommitListener) throws Exception
```

**Example :**
```java
import com.bobblekeyboard.ime.BobbleIME;
import com.bobblekeyboard.ime.BobbleIMETheme;

class CustomIME extends BobbleIME implements BobbleWordCommitListener {
    @Override
    public void onStartIME() {
        addWordCommitListener(this);
        super.onStartIME();
    }
    
    @Override
    public void onFinishIME() {
        // NOTE : Please don't do long running task here, you should move long running tasks to other thread
        removeWordCommitListener(this);
        super.onFinishIME();
    }
    
    @Override
    public void onWordCommit(String word) {
         // NOTE : Please don't do long running task here, you should move long running tasks to other thread
    
         Log.d("CustomIME onWordCommit", word);
    }
}
```

