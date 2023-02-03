# AndroidStudio-Lists_Cards_Colors

<details>

**<summary>Application Images</summary>**

<img src="resForReadme/mobile.gif">

</details>

## **Code**

<ul>

### <li>**Java files**

<ul>

<li>

<details>

**<summary>`DetailActivity.java`</summary>**

```java
package com.example.lists_cards_colors;

import android.os.Bundle;

import androidx.appcompat.app.AppCompatActivity;

import android.widget.ImageView;
import android.widget.TextView;

import com.bumptech.glide.Glide;

import androidx.fragment.app.FragmentActivity;

import com.example.lists_cards_colors.R;

/***
 * Detail Activity that is launched when a list item is clicked.
 * It shows more info on the sport.
 */
public class DetailActivity extends AppCompatActivity {

    /**
     * Initializes the activity, filling in the data from the Intent.
     *
     * @param savedInstanceState Contains information about the saved state
     *                           of the activity.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_detail);

        // Initialize the views.
        TextView sportsTitle = findViewById(R.id.titleDetail);
        ImageView sportsImage = findViewById(R.id.sportsImageDetail);

        // Set the text from the Intent extra.
        sportsTitle.setText(getIntent().getStringExtra("title"));

        // Load the image using the Glide library and the Intent extra.
        Glide.with(this).load(getIntent().getIntExtra("image_resource", 0))
                .into(sportsImage);
    }
}
```

</details>

</li>

<li>

<details>

**<summary>`MainActivity.java`</summary>**

```java
package com.example.lists_cards_colors;

import android.content.res.TypedArray;
import android.os.Bundle;
//import android.support.v7.app.AppCompatActivity;
import androidx.appcompat.app.AppCompatActivity;
//import android.support.v7.widget.GridLayoutManager;
//import android.support.v7.widget.LinearLayoutManager;
//import android.support.v7.widget.RecyclerView;
//import android.support.v7.widget.helper.ItemTouchHelper;
import androidx.recyclerview.widget.GridLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import androidx.recyclerview.widget.ItemTouchHelper;
import androidx.recyclerview.widget.LinearLayoutManager;

import android.view.View;

import com.example.lists_cards_colors.R;

import java.util.ArrayList;
import java.util.Collections;

/***
 * Main Activity for the Material Me app, a mock sports news application.
 */
public class MainActivity extends AppCompatActivity {

    // Member variables.
    private RecyclerView mRecyclerView;
    private ArrayList<Sport> mSportsData;
    private SportsAdapter mAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Initialize the RecyclerView.
        mRecyclerView = findViewById(R.id.recyclerView);

        int gridColumnCount = getResources().getInteger(R.integer.grid_column_count);

        // Set the Layout Manager.
        mRecyclerView.setLayoutManager(new GridLayoutManager(this, gridColumnCount));

        // Initialize the ArrayList that will contain the data.
        mSportsData = new ArrayList<>();

        // Initialize the adapter and set it to the RecyclerView.
        mAdapter = new SportsAdapter(this, mSportsData);
        mRecyclerView.setAdapter(mAdapter);

        // Get the data.
        initializeData();


        int swipeDirs;
        if (gridColumnCount > 1) {
            swipeDirs = 0;
        } else {
            swipeDirs = ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT;
        }

        // Helper class for creating swipe to dismiss and drag and drop
        // functionality.
        ItemTouchHelper helper = new ItemTouchHelper(new ItemTouchHelper.SimpleCallback(
                ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT | ItemTouchHelper.DOWN | ItemTouchHelper.UP, swipeDirs) {

            /**
             * Defines the drag and drop functionality.
             *
             * @param recyclerView The RecyclerView that contains the list items
             * @param viewHolder The SportsViewHolder that is being moved
             * @param target The SportsViewHolder that you are switching the
             *               original one with.
             * @return true if the item was moved, false otherwise
             */
            @Override
            public boolean onMove(RecyclerView recyclerView,
                                  RecyclerView.ViewHolder viewHolder,
                                  RecyclerView.ViewHolder target) {
                // Get the from and to positions.
                int from = viewHolder.getAdapterPosition();
                int to = target.getAdapterPosition();

                // Swap the items and notify the adapter.
                Collections.swap(mSportsData, from, to);
                mAdapter.notifyItemMoved(from, to);
                return true;
            }

            /**
             * Defines the swipe to dismiss functionality.
             *
             * @param viewHolder The viewholder being swiped.
             * @param direction The direction it is swiped in.
             */
            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder,
                                 int direction) {
                // Remove the item from the dataset.
                mSportsData.remove(viewHolder.getAdapterPosition());
                // Notify the adapter.
                mAdapter.notifyItemRemoved(viewHolder.getAdapterPosition());
            }
        });

        // Attach the helper to the RecyclerView.
        helper.attachToRecyclerView(mRecyclerView);
    }

    /**
     * Initialize the sports data from resources.
     */
    private void initializeData() {
        // Get the resources from the XML file.
        String[] sportsList = getResources()
                .getStringArray(R.array.sports_titles);
        String[] sportsInfo = getResources()
                .getStringArray(R.array.sports_info);
        TypedArray sportsImageResources = getResources()
                .obtainTypedArray(R.array.sports_images);

        // Clear the existing data (to avoid duplication).
        mSportsData.clear();

        // Create the ArrayList of Sports objects with the titles and
        // information about each sport
        for (int i = 0; i < sportsList.length; i++) {
            mSportsData.add(new Sport(sportsList[i], sportsInfo[i],
                    sportsImageResources.getResourceId(i, 0)));
        }

        // Recycle the typed array.
        sportsImageResources.recycle();

        // Notify the adapter of the change.
        mAdapter.notifyDataSetChanged();
    }

    /**
     * onClick method for th FAB that resets the data.
     *
     * @param view The button view that was clicked.
     */
    public void resetSports(View view) {
        initializeData();
    }
}
```

</details>

</li>

<li>

<details>

**<summary>`Sport.java`</summary>**

```java
package com.example.lists_cards_colors;

public class Sport {

    // Member variables representing the title and information about the sport.
    private String title;
    private String info;
    private final int imageResource;

    /**
     * Constructor for the Sport data model.
     *
     * @param title The name if the sport.
     * @param info  Information about the sport.
     */
    public Sport(String title, String info, int imageResource) {
        this.title = title;
        this.info = info;
        this.imageResource = imageResource;
    }

    /**
     * Gets the title of the sport.
     *
     * @return The title of the sport.
     */
    String getTitle() {
        return title;
    }

    /**
     * Gets the info about the sport.
     *
     * @return The info about the sport.
     */
    String getInfo() {
        return info;
    }

    public int getImageResource() {
        return imageResource;
    }


}
```

</details>

</li>

<li>

<details>

**<summary>`SportsAdapter.java`</summary>**

```java
package com.example.lists_cards_colors;

import android.content.Context;
import android.content.Intent;
//import android.support.v7.widget.RecyclerView;
import androidx.recyclerview.widget.RecyclerView;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import com.bumptech.glide.Glide;
import com.example.lists_cards_colors.R;

import java.util.ArrayList;

/***
 * The adapter class for the RecyclerView, contains the sports data.
 */
class SportsAdapter extends RecyclerView.Adapter<SportsAdapter.ViewHolder> {

    // Member variables.
    private ArrayList<Sport> mSportsData;
    private Context mContext;

    /**
     * Constructor that passes in the sports data and the context.
     *
     * @param sportsData ArrayList containing the sports data.
     * @param context    Context of the application.
     */
    SportsAdapter(Context context, ArrayList<Sport> sportsData) {
        this.mSportsData = sportsData;
        this.mContext = context;
    }


    /**
     * Required method for creating the viewholder objects.
     *
     * @param parent   The ViewGroup into which the new View will be added
     *                 after it is bound to an adapter position.
     * @param viewType The view type of the new View.
     * @return The newly created ViewHolder.
     */
    @Override
    public SportsAdapter.ViewHolder onCreateViewHolder(
            ViewGroup parent, int viewType) {
        return new ViewHolder(LayoutInflater.from(mContext).
                inflate(R.layout.list_item, parent, false));
    }

    /**
     * Required method that binds the data to the viewholder.
     *
     * @param holder   The viewholder into which the data should be put.
     * @param position The adapter position.
     */
    @Override
    public void onBindViewHolder(SportsAdapter.ViewHolder holder,
                                 int position) {
        // Get current sport.
        Sport currentSport = mSportsData.get(position);

        // Populate the textviews with data.
        holder.bindTo(currentSport);
    }

    /**
     * Required method for determining the size of the data set.
     *
     * @return Size of the data set.
     */
    @Override
    public int getItemCount() {
        return mSportsData.size();
    }


    /**
     * ViewHolder class that represents each row of data in the RecyclerView.
     */
    class ViewHolder extends RecyclerView.ViewHolder
            implements View.OnClickListener {

        // Member Variables for the TextViews
        private TextView mTitleText;
        private TextView mInfoText;
        private ImageView mSportsImage;

        /**
         * Constructor for the ViewHolder, used in onCreateViewHolder().
         *
         * @param itemView The rootview of the list_item.xml layout file.
         */
        ViewHolder(View itemView) {
            super(itemView);

            // Initialize the views.
            mTitleText = itemView.findViewById(R.id.title);
            mInfoText = itemView.findViewById(R.id.subTitle);
            mSportsImage = itemView.findViewById(R.id.sportsImage);

            // Set the OnClickListener to the entire view.
            itemView.setOnClickListener(this);
        }

        void bindTo(Sport currentSport) {
            // Populate the textviews with data.
            mTitleText.setText(currentSport.getTitle());
            mInfoText.setText(currentSport.getInfo());

            // Load the images into the ImageView using the Glide library.
            Glide.with(mContext).load(
                    currentSport.getImageResource()).into(mSportsImage);
        }

        /**
         * Handle click to show DetailActivity.
         *
         * @param view View that is clicked.
         */
        @Override
        public void onClick(View view) {
            Sport currentSport = mSportsData.get(getAdapterPosition());
            Intent detailIntent = new Intent(mContext, DetailActivity.class);
            detailIntent.putExtra("title", currentSport.getTitle());
            detailIntent.putExtra("image_resource",
                    currentSport.getImageResource());
            mContext.startActivity(detailIntent);
        }
    }
}
```

</details>

</li>

</ul>

</li>

### <li>**XML files**

<ul>

#### <li>**`drawable`**

<ul>

<li>

<details>

**<summary>`ic_reset.xml`</summary>**

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24.0"
    android:viewportHeight="24.0">
    <path
        android:fillColor="@color/black"
        android:pathData="M17.65,6.35C16.2,4.9 14.21,4 12,4c-4.42,0 -7.99,3.58 -7.99,8s3.57,8 7.99,8c3.73,0 6.84,-2.55 7.73,-6h-2.08c-0.82,2.33 -3.04,4 -5.65,4 -3.31,0 -6,-2.69 -6,-6s2.69,-6 6,-6c1.66,0 3.14,0.69 4.22,1.78L13,11h7V4l-2.35,2.35z" />
</vector>

```

</details>

</li>

</ul>

</li>

#### <li>**`layout`**

<ul>

<li>

<details>

**<summary>`activity_detail.xml`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <RelativeLayout xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:context="com.example.android.materialme.DetailActivity">

        <ImageView
            android:id="@+id/sportsImageDetail"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true" />

        <TextView
            android:id="@+id/titleDetail"
            style="@style/TextAppearance.AppCompat.Headline"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignBottom="@id/sportsImageDetail"
            android:padding="16dp"
            android:text="@string/title_placeholder"
            android:theme="@style/ThemeOverlay.AppCompat.Dark" />

        <TextView
            android:id="@+id/newsTitleDetail"
            style="@style/SportsDetailText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/sportsImageDetail"
            android:padding="16dp"
            android:text="@string/news_placeholder"
            android:textColor="?android:textColorSecondary" />

        <TextView
            android:id="@+id/subTitleDetail"
            style="@style/SportsDetailText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/newsTitleDetail"
            android:padding="16dp"
            android:text="@string/subtitle_detail_text" />

    </RelativeLayout>
</ScrollView>
```

</details>

</li>

<li>

<details>

**<summary>`activity_main.xml`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context="com.example.lists_cards_colors.MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_alignParentBottom="true"
        android:layout_margin="16dp"
        android:backgroundTint="@color/colorAccent"
        android:onClick="resetSports"
        app:srcCompat="@drawable/ic_reset"
        app:tint="@color/white" />

</RelativeLayout>

```

</details>

</li>

<li>

<details>

**<summary>`list_item.xml`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <ImageView
            android:id="@+id/sportsImage"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true" />

        <TextView
            android:id="@+id/title"
            style="@style/SportsTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignBottom="@id/sportsImage"
            android:padding="8dp"
            android:text="@string/title_placeholder"
            android:theme="@style/ThemeOverlay.AppCompat.Dark" />

        <TextView
            android:id="@+id/newsTitle"
            style="@style/SportsDetailTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/sportsImage"
            android:padding="8dp"
            android:text="@string/news_placeholder"
            android:textColor="?android:textColorSecondary" />

        <TextView
            android:id="@+id/subTitle"
            style="@style/SportsDetailText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/newsTitle"
            android:padding="8dp"
            android:text="@string/sports_info_placeholder" />

    </RelativeLayout>
</androidx.cardview.widget.CardView>
```

</details>

</li>

</ul>

#### <li>**`values`**

<ul>

<li>

<details>

**<summary>`colors.xml`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="purple_200">#FFBB86FC</color>
    <color name="purple_500">#FF6200EE</color>
    <color name="purple_700">#FF3700B3</color>
    <color name="teal_200">#FF03DAC5</color>
    <color name="teal_700">#FF018786</color>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>

    <color name="colorPrimary">#607D8B</color>
    <color name="colorPrimaryDark">#37474F</color>
    <color name="colorAccent">#FF6E40</color>
</resources>

```

</details>

</li>

<li>

<details>

**<summary>`dimens.xml`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Default screen margins, per the Android Design guidelines. -->
    <dimen name="activity_horizontal_margin">16dp</dimen>
    <dimen name="activity_vertical_margin">16dp</dimen>
</resources>

```

</details>

</li>

#### <li>**`integers.xml`**

<ul>

<li>

<details>

**<summary>`default`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <integer name="grid_column_count">1</integer>
</resources>
```

</details>

</li>

<li>

<details>

**<summary>`land`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <integer name="grid_column_count">2</integer>
</resources>
```

</details>

</li>

<li>

<details>

**<summary>`sw600dp`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <integer name="grid_column_count">3</integer>
</resources>
```

</details>

</li>

</ul>

</li>

#### <li>**`strings.xml`**

<ul>

<li>

<details>

**<summary>`default`</summary>**

```xml
<resources>
    <string name="app_name">Lists_Cards_Colors</string>
    <string name="title_placeholder">Title</string>
    <string name="news_placeholder">News</string>
    <string name="sports_info_placeholder">Here is some news</string>
    <string name="subtitle_detail_text">Lorem ipsum dolor sit amet,
        consectetur adipiscing elit. Praesent ultrices congue rutrum.
        Phasellus elementum ipsum ac convallis aliquam. Suspendisse eleifend
        eros a enim faucibus mollis. Nunc placerat, est vitae vestibulum
        blandit, dolor diam fringilla tellus, eu euismod mauris neque
        at neque.</string>

    <string-array name="sports_titles">
        <item>Baseball</item>
        <item>Badminton</item>
        <item>Basketball</item>
        <item>Bowling</item>
        <item>Cycling</item>
        <item>Golf</item>
        <item>Running</item>
        <item>Football</item>
        <item>Swimming</item>
        <item>Table Tennis</item>
        <item>Tennis</item>
    </string-array>

    <string-array name="sports_info">
        <item>Here is some Baseball news!</item>
        <item>Here is some Badminton news!</item>
        <item>Here is some Basketball news!</item>
        <item>Here is some Bowling news!</item>
        <item>Here is some Cycling news!</item>
        <item>Here is some Golf news!</item>
        <item>Here is some Running news!</item>
        <item>Here is some Football news!</item>
        <item>Here is some Swimming news!</item>
        <item>Here is some Table Tennis news!</item>
        <item>Here is some Tennis news!</item>
    </string-array>


    <array name="sports_images">
        <item>@drawable/img_baseball</item>
        <item>@drawable/img_badminton</item>
        <item>@drawable/img_basketball</item>
        <item>@drawable/img_bowling</item>
        <item>@drawable/img_cycling</item>
        <item>@drawable/img_golf</item>
        <item>@drawable/img_running</item>
        <item>@drawable/img_soccer</item>
        <item>@drawable/img_swimming</item>
        <item>@drawable/img_tabletennis</item>
        <item>@drawable/img_tennis</item>

    </array>
</resources>
```

</details>

</li>

<li>

<details>

**<summary>`en`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Lists_Cards_Colors</string>
    <string name="title_placeholder">Title</string>
    <string name="news_placeholder">News</string>
    <string name="sports_info_placeholder">Here is some news</string>
    <string name="subtitle_detail_text">Lorem ipsum dolor sit amet,
        consectetur adipiscing elit. Praesent ultrices congue rutrum.
        Phasellus elementum ipsum ac convallis aliquam. Suspendisse eleifend
        eros a enim faucibus mollis. Nunc placerat, est vitae vestibulum
        blandit, dolor diam fringilla tellus, eu euismod mauris neque
        at neque.</string>

    <string-array name="sports_titles">
        <item>Baseball</item>
        <item>Badminton</item>
        <item>Basketball</item>
        <item>Bowling</item>
        <item>Cycling</item>
        <item>Golf</item>
        <item>Running</item>
        <item>Soccer</item>
        <item>Swimming</item>
        <item>Table Tennis</item>
        <item>Tennis</item>
    </string-array>

    <string-array name="sports_info">
        <item>Here is some Baseball news!</item>
        <item>Here is some Badminton news!</item>
        <item>Here is some Basketball news!</item>
        <item>Here is some Bowling news!</item>
        <item>Here is some Cycling news!</item>
        <item>Here is some Golf news!</item>
        <item>Here is some Running news!</item>
        <item>Here is some Soccer news!</item>
        <item>Here is some Swimming news!</item>
        <item>Here is some Table Tennis news!</item>
        <item>Here is some Tennis news!</item>
    </string-array>


    <array name="sports_images">
        <item>@drawable/img_baseball</item>
        <item>@drawable/img_badminton</item>
        <item>@drawable/img_basketball</item>
        <item>@drawable/img_bowling</item>
        <item>@drawable/img_cycling</item>
        <item>@drawable/img_golf</item>
        <item>@drawable/img_running</item>
        <item>@drawable/img_soccer</item>
        <item>@drawable/img_swimming</item>
        <item>@drawable/img_tabletennis</item>
        <item>@drawable/img_tennis</item>

    </array>
</resources>
```

</details>

</li>

</ul>

#### <li>**`styles.xml`**

<ul>

<li>

<details>

**<summary>`default`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>


    <style name="SportsTitle" parent="TextAppearance.AppCompat.Display2" />

    <style name="SportsDetailTitle" parent="TextAppearance.AppCompat.Display1" />

    <style name="SportsDetailText" parent="TextAppearance.AppCompat.Subhead" />

</resources>
```

</details>

</li>

<li>

<details>

**<summary>`sw600dp`</summary>**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>


    <style name="SportsTitle" parent="TextAppearance.AppCompat.Display1">

        <item name="android:textColor">?android:textColorPrimary</item>

    </style>

    <style name="SportsDetailTitle" parent="TextAppearance.AppCompat.Headline" />

    <style name="SportsDetailText" parent="TextAppearance.AppCompat.Subhead" />


</resources>
```

</details>

</li>

</ul>

</ul>

</li>

</ul>

</li>

</ul>

</ul>
