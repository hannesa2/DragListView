# DragListView
DragListView can be used when you want to be able to re-order items in a list, grid or a board.

Youtube demo video<br>
[![Android drag and drop list and board](http://img.youtube.com/vi/tNgevYpyA9E/0.jpg)](https://www.youtube.com/watch?v=tNgevYpyA9E)

## Features
* Re-order items in a list, grid or board by dragging and dropping with nice animations.
* Add custom animations when the drag is starting and ending.
* Get a callback when a drag is started and ended with the position.

## Download lib with gradle

    repositories {
        mavenCentral()
    }

    dependencies {
        compile 'com.github.woxthebox:draglistview:1.0.8'
    }

## Usage
**NOTE: The adapter must use stable ids and only layout managers based on a LinearLayoutManager are supported.
List and Grid layouts are used as example in the sample project.

  For list and grid view use the DragListView.

        mDragListView = (DragListView) view.findViewById(R.id.drag_list_view);
        mDragListView.setDragListListener(new DragListView.DragListListener() {
                    @Override
                    public void onItemDragStarted(int position) {
                        Toast.makeText(getActivity(), "Start - position: " + position, Toast.LENGTH_SHORT).show();
                    }

                    @Override
                    public void onItemDragEnded(int fromPosition, int toPosition) {
                        if (fromPosition != toPosition) {
                            Toast.makeText(getActivity(), "End - position: " + toPosition, Toast.LENGTH_SHORT).show();
                        }
                    }
                });

        mDragListView.setLayoutManager(new LinearLayoutManager(getActivity()));
        ItemAdapter listAdapter = new ItemAdapter(mItemArray, R.layout.list_item, R.id.image, false);
        mDragListView.setAdapter(listAdapter);
        mDragListView.setCanDragHorizontally(false);

  A custom drag item can be provided to change the visual appearance of the dragging item.

        mDragListView.setCustomDragItem(new MyDragItem(getActivity(), R.layout.list_item));

        private static class MyDragItem extends DragItem {
            public MyDragItem(Context context, int layoutId) {
                super(context, layoutId);
            }

            @Override
            public void onBindDragView(View clickedView, View dragView) {
                CharSequence text = ((TextView) clickedView.findViewById(R.id.text)).getText();
                ((TextView) dragView.findViewById(R.id.text)).setText(text);
                dragView.setBackgroundColor(dragView.getResources().getColor(R.color.list_item_background));
            }
        }

  For a board, which is a number of horizontal columns with lists, then use BoardView. For an example with custom animations
  check the sample code.

        mBoardView = (BoardView) view.findViewById(R.id.board_view);
        mBoardView.setSnapToColumnsWhenScrolling(true);
        mBoardView.setSnapToColumnWhenDragging(true);
        mBoardView.setSnapDragItemToTouch(true);
        mBoardView.setBoardListener(new BoardView.BoardListener() {
              @Override
              public void onItemDragStarted(int column, int row) {
                  Toast.makeText(getActivity(), "Start - column: " + column + " row: " + row, Toast.LENGTH_SHORT).show();
              }

              @Override
              public void onItemChangedColumn(int oldColumn, int newColumn) {
                  TextView itemCount1 = (TextView) mBoardView.getHeaderView(oldColumn).findViewById(R.id.item_count);
                  itemCount1.setText("" + mBoardView.getAdapter(oldColumn).getItemCount());
                  TextView itemCount2 = (TextView) mBoardView.getHeaderView(newColumn).findViewById(R.id.item_count);
                  itemCount2.setText("" + mBoardView.getAdapter(newColumn).getItemCount());
              }

              @Override
              public void onItemDragEnded(int fromColumn, int fromRow, int toColumn, int toRow) {
                  if (fromColumn != toColumn || fromRow != toRow) {
                      Toast.makeText(getActivity(), "End - column: " + toColumn + " row: " + toRow, Toast.LENGTH_SHORT).show();
                  }
              }
        });
        ...
        mBoardView.addColumnList(listAdapter, header, false);


  For your adapter, extend DragItemAdapter and implement the methods below.
  You also need to provide a boolean to the super constructor to decide if you want the drag to happen on long press or directly when touching the item.

    private ArrayList<Pair<Long, String>> mItemList;  
    
    @Override
    public Object removeItem(int pos) {
        Object item = mItemList.remove(pos);
        notifyItemRemoved(pos);
        return item;
    }

    @Override
    public void addItem(int pos, Object item) {
        mItemList.add(pos, (Pair<Long, String>) item);
        notifyItemInserted(pos);
    }

    @Override
    public int getPositionForItemId(long id) {
        for (int i = 0; i < mItemList.size(); i++) {
            if (id == mItemList.get(i).first) {
                return i;
            }
        }
        return -1;
    }

    @Override
    public void changeItemPosition(int fromPos, int toPos) {
        Pair<Long, String> pair = mItemList.remove(fromPos);
        mItemList.add(toPos, pair);
        notifyDataSetChanged();
    }

  Your ViewHolder should extend DragItemAdapter.ViewHolder and you must supply an id of the view that should respond to a drag.
  
    public class ViewHolder extends DragItemAdapter.ViewHolder {
        public TextView mText;

        public ViewHolder(final View itemView) {
            super(itemView, mGrabHandleId);
            mText = (TextView) itemView.findViewById(R.id.text);
        }
    }

## License

If you use DragItemRecyclerView code in your application please inform the author about it (*email: woxthebox@gmail.com*) like this:
> **Subject:** DragListView usage notification<br />
> **Text:** I use DragListView in {application_name} - {http://link_to_google_play}.
> I [allow | don't allow] you to mention my app in section "Applications using DragListView" on GitHub.

    Copyright 2014 Magnus Woxblom

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
