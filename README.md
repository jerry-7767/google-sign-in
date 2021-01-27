# GoogleSignin
#### hey there,
#### I'm Rishi Android Developer
##### in this section you will learn how to authenticate user through google sign in with the help of firebase.
# ScreenShots
![googlesignmoc](https://user-images.githubusercontent.com/42550296/101642879-86066b00-3a59-11eb-9632-6fc0a4f01c63.png)
### Step 1:
#### Create a new Android Studio Project.
### Step 2:
#### Link Android Studio to Firebase
### Note:- You have to login with same email id (Firebase and Android Studio).
#### After linking android studio to firebase it will add dependency automatically.
### Step 3:
#### in "activity_login_screen.xml" file 
``` xml 
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/bg"
    tools:context=".LoginScreenActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:text="Welcome!"
        android:textColor="@color/colorPrimaryDark"
        android:textStyle="bold"
        android:textSize="50sp"
        android:layout_marginTop="30dp"
        android:fontFamily="@font/round_elegance"
        >
    </TextView>

    <Button
        android:id="@+id/btn_googleLogin"
        android:layout_width="250dp"
        android:layout_height="45dp"
        android:layout_centerHorizontal="true"
        android:drawableStart="@drawable/ic_google"
        android:layout_marginTop="25dp"
        android:paddingStart="25dp"
        android:layout_alignParentBottom="true"
        android:foreground="@drawable/ripple_color"
        android:layout_marginBottom="36dp"
        android:paddingEnd="25dp"
        android:text="Sign in with google"
        android:background="@drawable/button_custom"
        android:textAllCaps="false"
        android:textSize="16sp"
        android:elevation="10dp"
        android:textColor="@color/Black"
        android:drawableLeft="@drawable/ic_google" />

</RelativeLayout>
```
### Step 4:
#### in "LoginScreenActivity.java" file
``` java
package com.example.blog;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import com.google.android.gms.auth.api.signin.GoogleSignIn;
import com.google.android.gms.auth.api.signin.GoogleSignInAccount;
import com.google.android.gms.auth.api.signin.GoogleSignInClient;
import com.google.android.gms.auth.api.signin.GoogleSignInOptions;
import com.google.android.gms.common.api.ApiException;
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.auth.AuthCredential;
import com.google.firebase.auth.AuthResult;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.auth.FirebaseUser;
import com.google.firebase.auth.GoogleAuthProvider;

public class LoginScreenActivity extends AppCompatActivity {

    private Button mGoogleSignButton;
    private TextView tTerms;
    private GoogleSignInClient googleSignInClient;
    private final static int RC_SIGN_IN = 1001;
    private FirebaseAuth mAuth;
    private FirebaseAuth.AuthStateListener mFirebaseAuthStateListener;

    @Override
    protected void onStart() {
        super.onStart();
        mAuth.addAuthStateListener(mFirebaseAuthStateListener);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login_screen);

        mAuth = FirebaseAuth.getInstance();

        mFirebaseAuthStateListener = new FirebaseAuth.AuthStateListener() {
            @Override
            public void onAuthStateChanged(@NonNull FirebaseAuth firebaseAuth) {
                if (firebaseAuth.getCurrentUser() != null)
                {
                    startActivity(new Intent(LoginScreenActivity.this, HomeActivity.class));
                }
            }
        };

        mGoogleSignButton = findViewById(R.id.btn_googleLogin);
        mGoogleSignButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                switch (v.getId())
                {
                    case R.id.btn_googleLogin:
                        signin();
                        break;
                }
            }
        });
        Createrequest();
    }

    private void Createrequest()
    {      
        GoogleSignInOptions gso = new GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
                .requestIdToken("67661485405-fkade18rvj59lqops2fbi7rjrc0t2266.apps.googleusercontent.com")
                .requestEmail()
                .build();
        googleSignInClient = GoogleSignIn.getClient(this, gso);
    }

    public void signin()
    {
        Intent intent = googleSignInClient.getSignInIntent();
        startActivityForResult(intent, RC_SIGN_IN);
    }

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        // Result returned from launching the Intent from GoogleSignInApi.getSignInIntent(...);
        if (requestCode == RC_SIGN_IN) {
            Task<GoogleSignInAccount> task = GoogleSignIn.getSignedInAccountFromIntent(data);
            try {
                // Google Sign In was successful, authenticate with Firebase
                GoogleSignInAccount account = task.getResult(ApiException.class);
                firebaseAuthWithGoogle(account.getIdToken());
            } catch (ApiException e) {
                // Google Sign In failed, update UI appropriately
                Toast.makeText(LoginScreenActivity.this, "Google sign in failed", Toast.LENGTH_SHORT).show();
            }
        }
    }

    private void firebaseAuthWithGoogle(String idToken) {
        AuthCredential credential = GoogleAuthProvider.getCredential(idToken, null);
        mAuth.signInWithCredential(credential)
                .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
                    @Override
                    public void onComplete(@NonNull Task<AuthResult> task) {
                        if (task.isSuccessful()) {
                            // Sign in success, update UI with the signed-in user's information
                            FirebaseUser user = mAuth.getCurrentUser();
                            gotoProfile();
                        } else {
                            // If sign in fails, display a message to the user.
                            Toast.makeText(LoginScreenActivity.this, "Authentication Failed", Toast.LENGTH_SHORT).show();
                        }
                    }
                });
    }

    private void gotoProfile(){
        Intent intent=new Intent(LoginScreenActivity.this,HomeActivity.class);
        startActivity(intent);
        finish();
    }

}
```
