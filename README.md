# MigratingToJetpackCompose
Adding Jetpack Compose to your app 

If you want to use Jetpack Compose in an existing project, you’ll need to configure your project with required settings and dependencies.
Set up your development environment
Install the right Android Studio version

buildscript {
    ...
    dependencies {
        classpath "com.android.tools.build:gradle:7.0.0"
        ...
    }
}


Configure Kotlin
Make sure you're using Kotlin 1.5.21 in your project:

plugins {
    id 'org.jetbrains.kotlin:android' version '1.5.21'
}

Configure Gradle
You need to set your app’s minimum API level to 21 or higher and enable Jetpack Compose in your app's build.gradle file, as shown below. Also set the version for the Kotlin compiler plugin.

android {
    defaultConfig {
        ...
        minSdkVersion 21
    }

    buildFeatures {
        // Enables Jetpack Compose for this module
        compose true
    }
    ...

    // Set both the Java and Kotlin compilers to target Java 8.
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }

    composeOptions {
        kotlinCompilerExtensionVersion '1.0.1'
    }
}


Add Jetpack Compose toolkit dependencies
Include Jetpack Compose toolkit dependencies in your app’s build.gradle file, as shown below:

dependencies {
    // Integration with activities
    implementation 'androidx.activity:activity-compose:1.3.1'
    // Compose Material Design
    implementation 'androidx.compose.material:material:1.0.1'
    // Animations
    implementation 'androidx.compose.animation:animation:1.0.1'
    // Tooling support (Previews, etc.)
    implementation 'androidx.compose.ui:ui-tooling:1.0.1'
    // Integration with ViewModels
    implementation 'androidx.lifecycle:lifecycle-viewmodel-compose:1.0.0-alpha07'
    // UI Tests
    androidTestImplementation 'androidx.compose.ui:ui-test-junit4:1.0.1'
}



Reuse your View theme in Compose
If you just added Compose to your project, you might want Compose to inherit the themes available in the View system instead of rewriting your own Material theme in Compose from scratch.

If you're using the MDC library in your Android app, the MDC Compose Theme Adapter library allows you to easily re-use the color, typography and shape theming from your existing View-based themes, in your composables. That's achieved using the MdcTheme API.

If you're using AppCompat XML themes instead, use the AppCompat Compose Theme Adapter that contains the AppCompatTheme API.

Include the dependency you need in your app’s build.gradle file, as shown below:

dependencies {
    // When using a MDC theme
    implementation "com.google.android.material:compose-theme-adapter:1.0.1"

    // When using a AppCompat theme
    implementation "com.google.accompanist:accompanist-appcompat-theme:0.16.0"
}



To migrate it to Compose, we can replace the View with a ComposeView keeping the same layout parameters and id:

<...>
    <!-- Other content -->

    <androidx.compose.ui.platform.ComposeView
        android:id="@+id/greeting"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>
</...>


Then, in either the Activity or Fragment that uses that XML layout, we get the ComposeView from it and call the setContent method to add Compose content into it:

class MyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ...

        val greeting = findViewById<ComposeView>(R.id.greeting)
        greeting.setContent {
            MdcTheme { // or AppCompatTheme
                Greeting()
            }
        }
    }
}

@Composable
private fun Greeting() {
    Text(
        text = stringResource(R.string.greeting),
        style = MaterialTheme.typography.h5,
        modifier = Modifier
            .fillMaxWidth()
            .padding(horizontal = dimensionResource(R.dimen.margin_small))
            .wrapContentWidth(Alignment.CenterHorizontally)
    )
}
