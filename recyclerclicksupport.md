# RecyclerClickSupport

方便我們在現行的 RecyclerView 中實現 item click listener  & item long click listener

>這個套件是參考[這篇](http://www.littlerobots.nl/blog/Handle-Android-RecyclerView-Clicks/)的做法進行實作

##使用
```java
    //  Click Listener
    RecyclerClickSupport.addTo(recyclerView).setOnItemClickListener(new RecyclerClickSupport.OnItemClickListener() {
            @Override
            public void onItemClicked(RecyclerView recyclerView, int position, View v) {
                /*  do something    */
            }
        });
        
    //  Long Click Listener
    RecyclerClickSupport.addTo(recyclerView).setOnItemLongClickListener(new RecyclerClickSupport.OnItemLongClickListener() {
            @Override
            public boolean onItemLongClicked(RecyclerView recyclerView, int position, View v) {
                /*  do something    */
                return true;    // true if the callback consumed the long click, false otherwise.
            }
        });
```