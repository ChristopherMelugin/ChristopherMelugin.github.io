# Welcome to Christopher Melugin's GitHub Page and ePortfolio

### ASSESSMENT OF VALUES
Throughout the pursuance of my Computer Science degree in an online-only learning environment, I have honed my **self-motivation and attention to detail**, which is evidenced by my **3.98 GPA**. Throughout this time and since the year 2014, I have been working full time to support my family which has required a great deal of **dedication** and no small amount of sacrifice to achieve my educational goals. My combined experiences have taught me that **professionalism, communication, respect, honesty, and quality** are among the greatest assets a company can have in an employee, and I strongly believe I have cultivated these qualities to great effect. 

### ASSESSMENT OF SKILLS AND KNOWLEDGE
Within Computer Science and the development of this portfolio, I have demonstrated mastery over the fundamentals of each of the following:

- **data structures and algorithms** to handle computations in an efficient, stable, and safe manner

- **software engineering** including good design decisions with the long term kept foremost in mind

- **software architecture** to ensure smooth, intuitive development

- **database operations** that are clean and effective

- **version control** to keep projects organized and maintain efficient team collaboration.

- **general security** to keep people and data safe and prevent undefined behavior.

### RESUME
A more complete form of my employable skills and experience are contained in my [resume](Resume 2021/Christopher Kent Melugin.pdf).

### INTRODUCTION TO ENHANCEMENTS AND EXAMPLES
In my Mobile Architecture & Programming class I developed a simple Android application on my own using Java and XML to manage an inventory. I selected this project to enhance as my capstone project because I feel like I got a clear idea of what it is like to be a software engineer by building this app. At the time I was struggling, feeling like I didn't know enough to be successful when the time came to find employment. Completing this project initially from start to finish really bolstered my confidence and taught me a great deal. 
For this portfolio I have enhanced and iterated on this fully functional application to demonstrate my competency and abilities in various categories which are: code reviews to evaluate current code conditions and identify improvements, software engineering and design through implementing a new way to interact with the inventory items, data structures and algorithms through a sorting function and database operations by implementing tagging and filtering system. Here is a link to the [repository](https://github.com/ChristopherMelugin/Inventory_App) for the Inventory app.

###### Here is a visualization of the features the app had for the Mobilie Architecture and Programming class

| Login and Update Quantities  | Adding and Deleting Items |
| :------------- | :------------- |
|![Login and update quantities](Gifs/Login_update_qty.gif "Shows the original app functionality for logging in and updating quantities of an item")|![Adding and deleting items](Gifs/Add_delete.gif "Shows the original app functionality for adding and deleting items")|

### CODE REVIEW

Below is a code review I conducted of my own project. I cover how the Inventory app functions, the code associated with it, and some planned enhancements and improvements.
The external link is [here](https://www.youtube.com/watch?v=QE6oGewLaLA).

<iframe width="560" height="315" src="https://www.youtube.com/embed/QE6oGewLaLA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### SOFTWARE ENGINEERING AND DESIGN ENHANCEMENT

| Modify Items |
| :------------- |
|![Modify Items](Gifs/Modify.gif "Long clicking an item pops up a screen allowing for modifications")|

This enhancement was designed to create the capabliity for the user to directly modify an item's name and quantity. In creating this enhancement, I encountered an issue with the app crashing when no value was input into the popup view’s form. I discovered that the app is lacking somewhat in input validation and implemented a check for that. Throughout the process, I learned more about how both the data flows and also how the android platform flows, and as such, unearthed an easier path to implement my plans. 

For example, when trying to get the popup view to initially display the item’s current name and quantity values in the editable fields, I struggled to find a solution. My first thought was to set and then change a string constant in the resources file, only to discover that that is impossible to alter that file at runtime, and instead found a much simpler way by just using a simple call to set the text: `popup_item_name.setText(item.getTitle());` . This helped remind me that there is often a simple solution to the issues you are facing, and that a simple solution is more often than not better than a complicated solution.

###### Here is the bulk of what was added for the software engineering/design enhancement. (Does not include any UI updates)

```java
 public void onItemLongClick(InventoryItem item) {
        mInventoryItem = item;
        List<Tag> tags = mDb.getTags(mUsername);
        dialogBuilder = new AlertDialog.Builder(this);
        final View inventoryPopupView = getLayoutInflater().inflate(R.layout.inventory_popup, null);
        popup_item_name = (EditText) inventoryPopupView.findViewById(R.id.popup_item_name);
        popup_item_qty = (EditText) inventoryPopupView.findViewById(R.id.popup_item_qty);
        save_mods = (Button) inventoryPopupView.findViewById(R.id.popup_save);

        // Get the tag that is mapped to the item
        int mappedTag = mDb.getMapTag(String.valueOf(item.getId()));
        Tag retrievedTag = mDb.getTagForPopup(mUsername, String.valueOf(mappedTag));

        // Define and build spinner for tags
        Spinner spinner = (Spinner) inventoryPopupView.findViewById(R.id.tag_list);
        ArrayAdapter<Tag> adapter = new ArrayAdapter<>(getApplicationContext(), android.R.layout.simple_spinner_dropdown_item, tags);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spinner.setAdapter(adapter);

        // Sets item's tag as visible when popup is loaded
        spinner.setSelection(adapter.getPosition(retrievedTag));

        // Behavior for when an item is clicked in the spinner
        spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            // For selecting options in the Spinner
            public void onItemSelected(AdapterView<?> parent, View view, int pos, long id) {
                tagId = mDb.getTagForSelections(mUsername, parent.getItemAtPosition(pos).toString());
            }
            @Override
            public void onNothingSelected(AdapterView<?> parent) {
            }
        });

        // Set visible fields to the selected items relevant properties
        popup_item_name.setText(item.getTitle());
        popup_item_qty.setText(String.valueOf(item.getQuantity()));

        dialogBuilder.setView(inventoryPopupView);
        dialog = dialogBuilder.create();
        dialog.show();

        save_mods.setOnClickListener(new View.OnClickListener(){

            @Override
            public void onClick(View view) {
                String name = popup_item_name.getText().toString();
                String quantity = popup_item_qty.getText().toString();
                if (!name.equals("")) {
                    mDb.updateItemName(mInventoryItem.getId(), name);
                }
                if (!quantity.equals("")) {
                    mDb.updateQuantity(mInventoryItem.getId(), Integer.parseInt(quantity));
                }
                mDb.newMap(mInventoryItem.getId(), tagId);
                dialog.dismiss();
                mAdapter.notifyItemChanged(mInventoryItem.getId());
                onResume();
                checkForLow(mInventoryItem);
            }
        });
    }
```

### DATA STRUCTURES AND ALGORITHMS ENHANCEMENT

| Sorting Items |
| :------------- |
|![Sorting Items](Gifs/Sorting.gif "Sorting by alphabet or by quantity")|

This enhancement was designed to increase the manipulability of the items by implementing two types of sorting. In creating this enhancement, I deliberated a little with how exactly to implement the sorting. I had a few choices, sorting as the list is being pulled from the database, or sorting the array list that the data resides in. A problem with the first choice was differentiating the property to sort by while simultaneously extracting it all from the database was a complicated process that I abandoned early. I eventually decided to set the UI buttons to toggle a respective Boolean value and then trigger the `onResume()` function to reload the items. During this process the Boolean values are checked and when found true, the list is sorted before populating into the view with the adapter. This way the sorting happens inline. I implemented some comparator functions to make sure that the right values were being checked for the sorts as well, leading to a fast and effective solution.

###### Here is the bulk of what was added for the data structures and algorithms enhancement. (Does not include any UI updates)

```java
private List<InventoryItem> loadInventory(String username, String filter) {
        List<InventoryItem> items;
        if (filter == null) {
            items = mDb.getInventoryItems(username);
        }
        else {
            items = mDb.getFilteredInventoryItems(username, filter);
        }
        if (sAbc == true) {
            Collections.sort(items, new compareTitles());
        }
        else if(sQty == true) {
            Collections.sort(items, new compareQty());
        }
        return items;
    }
    
     // Title sort button function
    public void sortAbc() {
        sAbc = !sAbc;
        sQty = false;
        onResume();
    }

    // Quantity sort button function
    public void sortQty() {
        sAbc = false;
        sQty = !sQty;
        onResume();
    }

    // Title comparator
    public static class compareTitles implements Comparator<InventoryItem> {
        @Override
        public int compare(InventoryItem item0, InventoryItem item1) {
            return item0.getTitle().compareTo(item1.getTitle());
        }
    }

    // Quantity comparator
    public static class compareQty implements Comparator<InventoryItem> {
        @Override
        public int compare(InventoryItem item0, InventoryItem item1) {
            return item1.getQuantity() - (item0.getQuantity());
        }
    }
```

### DATABASE ENHANCEMENT

| Tag System  | Filtering System |
| :------------- | :------------- |
|![Tag system](Gifs/Tag.gif "App restructured to include a tag for each item. Can add new tags and change an item's tag")|![Filter System](Gifs/Filter.gif "Using database operations, the items can be filtered by their tags")|

This app utilizes the SQLite database to manage and maintain the inventory items, and implements each of the create, read, update, and delete database operations to a thorough and efficient effect. The massive improvement that I made to the app that relates to the database is the full implementation of a single tag system (which has been set up to scale well to a multiple tag system) and the accompanying feature to filter the inventory items by any tag. This feature has been seamlessly melded into the rest of the app.

In creating this enhancement, I needed to start with the tag system before I could implement the filter. At first, I didn’t realize just how many extra hours it would take to incorporate an entire tag system into the app which I did just to demonstrate my advanced database abilities. A major challenge I faced was how to connect each item to a tag and opted for a separate mapping table to link each item in the item table to an entry in the tag table.  This made the query to get the filtered list trickier, but more robust in the end. Another challenge was having to restructure or rework much of the app for this new way of working with the data. Including multiple UI elements, their handling, and data flow and behavior because almost every part of the app now needed to handle tags.

###### Here is the function in the Database.java file that handles the database query to filter the list by a tag.

```java
    // Gets inventory items with a tag as a filter
    public List<InventoryItem> getFilteredInventoryItems(String username, String filter) {
        List<InventoryItem> items = new ArrayList<>();
        SQLiteDatabase db = this.getReadableDatabase();
        String sql = "SELECT * FROM "
                + ItemTagMappingTable.TABLE + " JOIN "
                + InventoryTable.TABLE + " ON "
                + ItemTagMappingTable.ITEM_REFERENCE + " = "
                + InventoryTable.TABLE + "."
                + InventoryTable.COL_ID + " JOIN "
                + TagTable.TABLE + " ON "
                + ItemTagMappingTable.TAG_REFERENCE + " = "
                + TagTable.TABLE + "."
                + TagTable.TAG_ID + " WHERE "
                + InventoryTable.TABLE + "."
                + InventoryTable.COL_USERNAME + " = ? AND "
                + TagTable.TAG_NAME + " = ?";

        Cursor cursor = db.rawQuery(sql, new String[] { username, filter });
        if(cursor.moveToFirst()) {
            do {
                InventoryItem item = new InventoryItem();
                item.setId(cursor.getInt(2));
                item.setTitle(cursor.getString(3));
                item.setQuantity(cursor.getInt(4));
                item.setNotifyOnLow(cursor.getInt(5));
                item.setUsername(cursor.getString(6));
                items.add(item);
            } while (cursor.moveToNext());
        }
        cursor.close();
        return items;
    }
```



