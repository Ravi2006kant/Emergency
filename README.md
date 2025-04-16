manifest

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/logo"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/logo"
        android:supportsRtl="true"
        android:theme="@style/Theme.Emergency"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity2"
            android:exported="false" />
        <activity
            android:name=".information"
            android:exported="false" />
        <activity
            android:name=".login"
            android:exported="true" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!--
             TODO: Before you run your application, you need a Google Maps API key.

             To get one, follow the directions here:

                https://developers.google.com/maps/documentation/android-sdk/get-api-key

             Once you have your API key (it starts with "AIza"), define a new property in your
             project's local.properties file (e.g. MAPS_API_KEY=Aiza...), and replace the
             "YOUR_API_KEY" string in this file with "${MAPS_API_KEY}".
        -->
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="@string/api" />

        <activity
            android:name=".MapsActivity"
            android:exported="false"
            android:label="@string/title_activity_maps" />
        <activity
            android:name=".MainActivity"
            android:exported="true"/>
    </application>

</manifest>


login:--

package com.example.emergency;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;

public class login extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
                setContentView(R.layout.activity_login);
        Button emer, sign;
        sign = findViewById(R.id.sign);
        emer = findViewById(R.id.emer);
        sign.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent next = new Intent(login.this,information.class);
                startActivity(next);
            }
        });

        emer.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent next = new Intent(login.this,MapsActivity.class);
                startActivity(next);
            }
        });
    }
}


main activity

package com.example.emergency;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Sign In button click
//        findViewById(R.id.btnSignIn).setOnClickListener(new View.OnClickListener() {
//            @Override
//            public void onClick(View v) {
//                Intent intent = new Intent(MainActivity.this, SignInActivity.class);
//                startActivity(intent);
//            }
//        });

        // Emergency button click
//        findViewById(R.id.btnEmergency).setOnClickListener(new View.OnClickListener() {
//            @Override
//            public void onClick(View v) {
//                Intent intent = new Intent(MainActivity.this, MapActivity.class);
//                startActivity(intent);
//            }
//        });

    }
}


map activity

package com.example.emergency;
import android.Manifest;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import android.content.Context;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.graphics.Color;
import android.location.Location;
import android.location.LocationManager;
import android.os.Bundle;
import android.provider.Settings;
import android.util.Log;
import android.view.View;
import android.widget.ProgressBar;
import android.widget.Toast;
import com.google.android.gms.location.FusedLocationProviderClient;
import com.google.android.gms.location.LocationServices;
import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.CircleOptions;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.MarkerOptions;
import com.google.android.gms.tasks.Task;

import org.json.JSONArray;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class MapsActivity extends AppCompatActivity implements OnMapReadyCallback {
    private static final int LOCATION_PERMISSION_REQUEST_CODE = 1;
    private FusedLocationProviderClient fusedLocationProviderClient;
    private GoogleMap mMap;
    ProgressBar progressBar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
         progressBar = findViewById(R.id.progressBar);
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        fusedLocationProviderClient = LocationServices.getFusedLocationProviderClient(this);
        requestLocationPermission();
        assert mapFragment != null;
        mapFragment.getMapAsync(this);
    }

    private void requestLocationPermission() {
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.ACCESS_FINE_LOCATION}, LOCATION_PERMISSION_REQUEST_CODE);
        } else {
            checkLocationEnabled();
        }
    }

    private void checkLocationEnabled() {
        LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
        if (locationManager != null && !locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)) {
            new AlertDialog.Builder(this)
                    .setMessage("Location is turned off. Please enable it to use this feature.")
                    .setPositiveButton("Settings", (dialog, which) -> {
                        startActivity(new Intent(Settings.ACTION_LOCATION_SOURCE_SETTINGS));
                    })
                    .setNegativeButton("Cancel", null)
                    .show();
        }
    }

    @Override
    public void onMapReady(GoogleMap mMap) {
        this.mMap = mMap;
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
            mMap.setMyLocationEnabled(true);
            getCurrentLocationAndSearchHospitals();
        }
    }

    private void getCurrentLocationAndSearchHospitals() {
        progressBar.setVisibility(View.VISIBLE); // Show spinner

        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            // TODO: Consider calling
            //    ActivityCompat#requestPermissions
            // here to request the missing permissions, and then overriding
            //   public void onRequestPermissionsResult(int requestCode, String[] permissions,
            //                                          int[] grantResults)
            // to handle the case where the user grants the permission. See the documentation
            // for ActivityCompat#requestPermissions for more details.
            return;
        }
        Task<Location> task = fusedLocationProviderClient.getLastLocation();
        task.addOnSuccessListener(location -> {
            progressBar.setVisibility(View.GONE); // Hide spinner after fetching location

            if (location != null) {
                LatLng currentLocation = new LatLng(location.getLatitude(), location.getLongitude());
                mMap.addMarker(new MarkerOptions().position(currentLocation).title("You are here"));
                mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(currentLocation, 15));
            } else {
                Toast.makeText(this, "Unable to fetch location", Toast.LENGTH_SHORT).show();
            }
        }).addOnFailureListener(e -> {
            progressBar.setVisibility(View.GONE); // Hide spinner on failure
            Toast.makeText(this, "Error fetching location", Toast.LENGTH_SHORT).show();
        });
    }


    private void searchNearestHospital(LatLng currentLocation, int radius) {
        String apiKey = "YOUR_GOOGLE_PLACES_API_KEY"; // Replace with your Google Places API Key
        String url = String.format(
                "@string/api",
                currentLocation.latitude, currentLocation.longitude, radius, apiKey
        );

        new Thread(() -> {
            try {
                // Fetch hospital data from Google Places API
                URL requestUrl = new URL(url);
                HttpURLConnection connection = (HttpURLConnection) requestUrl.openConnection();
                InputStreamReader reader = new InputStreamReader(connection.getInputStream());
                BufferedReader bufferedReader = new BufferedReader(reader);
                StringBuilder result = new StringBuilder();
                String line;
                while ((line = bufferedReader.readLine()) != null) {
                    result.append(line);
                }

                JSONObject responseJson = new JSONObject(result.toString());
                JSONArray resultsArray = responseJson.getJSONArray("results");

                if (resultsArray.length() > 0) {
                    JSONObject nearestHospital = resultsArray.getJSONObject(0);
                    double lat = nearestHospital.getJSONObject("geometry").getJSONObject("location").getDouble("lat");
                    double lng = nearestHospital.getJSONObject("geometry").getJSONObject("location").getDouble("lng");
                    String name = nearestHospital.getString("name");

                    runOnUiThread(() -> {
                        LatLng hospitalLocation = new LatLng(lat, lng);
                        mMap.addMarker(new MarkerOptions().position(hospitalLocation).title(name));
                        mMap.addCircle(new CircleOptions()
                                .center(currentLocation)
                                .radius(radius)
                                .fillColor(Color.argb(50, 255, 0, 0))
                                .strokeColor(Color.BLUE));
                        mMap.animateCamera(CameraUpdateFactory.newLatLngZoom(hospitalLocation, 15));
                    });
                } else {
                    // If no hospital is found within the radius, increase the radius and retry
                    if (radius < 10000) { // Set a reasonable maximum radius
                        searchNearestHospital(currentLocation, radius + 2000);
                    } else {
                        runOnUiThread(() -> Toast.makeText(this, "No hospitals found within 10 km", Toast.LENGTH_SHORT).show());
                    }
                }

            } catch (Exception e) {
                e.printStackTrace();
                runOnUiThread(() -> Toast.makeText(this, "Error fetching hospital data", Toast.LENGTH_SHORT).show());
                Log.d("Debug", "Fetching hospital details URL: " + url);
            }
        }).start();
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == LOCATION_PERMISSION_REQUEST_CODE) {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                checkLocationEnabled();
            } else {
                Toast.makeText(this, "Location permission denied", Toast.LENGTH_SHORT).show();
            }
        }
    }
}

//    public class MapsActivity extends AppCompatActivity implements OnMapReadyCallback {
//        private static final int LOCATION_PERMISSION_REQUEST_CODE = 1;
//        private FusedLocationProviderClient fusedLocationProviderClient;
//        @Override
//        protected void onCreate(Bundle savedInstanceState) {
//            super.onCreate(savedInstanceState);
//            setContentView(R.layout.activity_maps);
//            SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
//                    .findFragmentById(R.id.map);
//             fusedLocationProviderClient = LocationServices.getFusedLocationProviderClient(this);
//            requestLocationPermission();
//            assert mapFragment != null;
//            mapFragment.getMapAsync(this);
//        }
//        // to access the user location
//        private void requestLocationPermission() {
//            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED &&
//                    ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
//                ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION}, LOCATION_PERMISSION_REQUEST_CODE);
//            }}
//        //tasks to perform on the map
//            @Override
//            public void onMapReady(GoogleMap mMap) {
//            mMap.addMarker(new MarkerOptions().position(new LatLng(0, 0)).title("Marker"));
//            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
//                Task<Location> task = fusedLocationProviderClient.getLastLocation();
//
//                task.addOnSuccessListener(new OnSuccessListener<Location>() {
//                    @Override
//                    public void onSuccess(Location location) {
//                        if (location != null) {
//                            LatLng currentLocation = new LatLng(location.getLatitude(), location.getLongitude());
//                            //used to add the pin marker on the map
//                            mMap.addMarker(new MarkerOptions().position(currentLocation).title("You are here"));
//                            // used to move the camera of the map
//                            mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(currentLocation, 15));
//
//                            mMap.addCircle(new CircleOptions()
//                                    .center(currentLocation)
//                                    .radius(10000)
//                                    .fillColor(Color.RED)
//                                    .strokeColor(Color.BLUE));
//                        }
//                    }
//                });
//        }}}

//private void requestLocationPermission() {
//        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED &&
//                ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
//            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION}, LOCATION_PERMISSION_REQUEST_CODE);
//        }

//    @Override
//    public void onMapReady(@NonNull GoogleMap googleMap) {
//        mMap = googleMap;
//        LatLng ltnlng = new LatLng(23.254 , 20.254);
//        MarkerOptions markerOptions = new MarkerOptions().position(ltnlng).title("Gomoh");
//                mMap.addMarker(markerOptions);
//            mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(ltnlng, 15));
//    }
//}


corner :--
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle"
    >
    <corners android:radius="75dp"/>
    <solid android:color="#E91E63"/>

</shape>

layout of login

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".login">

<androidx.appcompat.widget.AppCompatButton
    android:id="@+id/sign"
    android:layout_width="250dp"
    android:layout_height="75dp"
    android:background="@drawable/corner"
    android:text="Sign-in"
    android:layout_gravity="center"
    android:textAlignment="center"
    android:layout_marginTop="250dp"
    android:textColor="@color/white"
    android:textSize="25sp"
    android:textStyle="bold"
/>
    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/emer"
        android:layout_width="250dp"
        android:layout_height="75dp"
        android:background="@drawable/corner"
        android:text="Emergency"
        android:layout_gravity="center"
        android:layout_marginTop="15dp"
        android:textAlignment="center"
        android:textColor="@color/white"
        android:textSize="25sp"
        android:textStyle="bold"
        />
</LinearLayout>
