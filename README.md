## Resultat


<p align="center">
  <img width="200px"src="https://i.ibb.co/LnfqGcn/Screenshot-2022-02-10-16-12-30-114-com-example-mylocationdemo.jpg" />
</p>

## Dependencies 
``` 
 implementation 'com.google.android.gms:play-services-location:19.0.1'

``` 

## Uses Permission

``` xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
``` 

## Java Code

``` java

package com.example.mylocationdemo;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.Context;
import android.content.IntentSender;
import android.content.pm.PackageManager;
import android.location.LocationManager;
import android.os.Build;
import android.os.Bundle;
import android.os.Looper;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import com.google.android.gms.common.api.ApiException;
import com.google.android.gms.common.api.ResolvableApiException;
import com.google.android.gms.location.LocationCallback;
import com.google.android.gms.location.LocationRequest;
import com.google.android.gms.location.LocationResult;
import com.google.android.gms.location.LocationServices;
import com.google.android.gms.location.LocationSettingsRequest;
import com.google.android.gms.location.LocationSettingsResponse;
import com.google.android.gms.location.LocationSettingsStatusCodes;
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;

public class MainActivity extends AppCompatActivity {

    Button btnGetLocation ;
    TextView tvLocation ;
    LocationRequest locationRequest ;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnGetLocation = findViewById(R.id.getLocation);
        tvLocation = findViewById(R.id.location);
        locationRequest = LocationRequest.create();
        locationRequest.setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY);
        locationRequest.setInterval(5000);
        locationRequest.setFastestInterval(2000);


        btnGetLocation.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
                    if(ActivityCompat.checkSelfPermission(MainActivity.this, Manifest.permission.ACCESS_FINE_LOCATION)== PackageManager.PERMISSION_GRANTED){
                        if(isGpsOn()){

                            //when we get location update
                            LocationServices.getFusedLocationProviderClient(MainActivity.this).requestLocationUpdates(locationRequest,new LocationCallback() {
                                @Override
                                public void onLocationResult(@NonNull LocationResult locationResult) {
                                    super.onLocationResult(locationResult);

                                    //remove location update
                                   // LocationServices.getFusedLocationProviderClient(MainActivity.this).removeLocationUpdates(this);
                                    if(locationResult != null && locationResult.getLocations().size() > 0){
                                       // int index = locationResult.getLocations().size() -1 ;
                                        double latitude = locationResult.getLastLocation().getLatitude();
                                        double longitude = locationResult.getLastLocation().getLongitude();
                                        tvLocation.setText("Latitude: "+latitude+"\nLongitude: "+longitude);
                                    }
                                }
                            },Looper.getMainLooper()) ;



                        }else{
                            turnOnGps();
                        }
                    }else{
                        requestPermissions(new String[]{Manifest.permission.ACCESS_FINE_LOCATION},2506);

                    }
                }
            }


        });


    }



    private void turnOnGps() {
        Toast.makeText(MainActivity.this, "1", Toast.LENGTH_SHORT).show();
        LocationSettingsRequest.Builder builder = new LocationSettingsRequest.Builder().addLocationRequest(locationRequest);
        builder.setAlwaysShow(true);

        Task<LocationSettingsResponse> result = LocationServices.getSettingsClient(getApplicationContext()).checkLocationSettings(builder.build());
        Toast.makeText(MainActivity.this, "2", Toast.LENGTH_SHORT).show();
        result.addOnCompleteListener(new OnCompleteListener<LocationSettingsResponse>() {
            @Override
            public void onComplete(@NonNull Task<LocationSettingsResponse> task) {

                try {
                    LocationSettingsResponse response = task.getResult(ApiException.class);
                    Toast.makeText(MainActivity.this, "GPS is already tured on", Toast.LENGTH_SHORT).show();

                } catch (ApiException e) {

                }
            }
        });



    }


    boolean isGpsOn(){
        boolean isOn = false ;
        LocationManager locationManager = null ;

        if(locationManager == null){
            locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);

        }

        isOn = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER) ;

        return  isOn ;
    }

}
```

## XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:id="@+id/navbar"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:background="@color/purple_700"
        android:orientation="horizontal"
        android:gravity="center"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Find Your location"
            android:textColor="@color/white"
            android:textSize="25dp"/>


    </LinearLayout>

    <TextView
        android:id="@+id/location"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toBottomOf="@id/navbar"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        android:text=".............."
        android:textSize="25dp"
        android:textColor="@color/purple_700"
        android:layout_marginTop="25dp"/>

    <Button
        android:id="@+id/getLocation"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toBottomOf="@id/location"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        android:layout_marginTop="25dp"
        android:text="Get My Location Now"/>



</androidx.constraintlayout.widget.ConstraintLayout>
```
