---
layout: post
title: "How to highlight the search term in results in an Android App"
date: 2014-12-08 08:40:11 -0500
comments: true
categories: programming android java
---

Few months ago I created an Android app [Al-Quran Bangla Mormobani](https://play.google.com/store/apps/details?id=quantum.org.quran) in several days for a quick deadline, so couldn't add much features to it. One such feature was the search terms were not highlighted in the results view so users couldn't use it effectively for reference. I got several requests from the users asking for this features. Few days ago I had couple of hours free to look into it and after searching for a while found a solution.

<!-- more -->

The data was coming from an Sqlite database and I am using a listview to present the results. As the adapter of the ```ListView``` I am using a ```SimpleCursorAdapter``` . Now to decorate specific terms in the ```ListViewItems``` I am using a ```viewBinder``` . The code is as follows,

``` java ViewBinder
adapter.setViewBinder(new SimpleCursorAdapter.ViewBinder() {
       public boolean setViewValue(View view, Cursor cursor, int columnIndex) {
           if (view.getId() == R.id.favorite_bangla_description) {
               String description = cursor.getString(cursor.getColumnIndex("bangla_description"));
               int startPos = description.indexOf(query);
               int endPos = description.indexOf(" ", startPos);
               if (startPos != -1) // This should always be true, just a sanity check
               {
                   Spannable spannable = new SpannableString(description);
                   ColorStateList blueColor = new ColorStateList(new int[][] { new int[] {}}, new int[] { Color.BLUE });
                   TextAppearanceSpan highlightSpan = new TextAppearanceSpan(null, Typeface.BOLD, -1, blueColor, null);
                   spannable.setSpan(highlightSpan, startPos, endPos, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
                   ((TextView) view).setText(spannable);
               }
               else
                   ((TextView) view).setText(description);
               return true;
           }
           return false;
       }
});

```

I am not in a mood to explain the nitty gritty of the whole code above in this post, but the main part which is doing the highlighting is the use of ```Spannable``` String. Or more precisely the following code,

``` java Highlighting Text

Spannable spannable = new SpannableString(description);
ColorStateList blueColor = new ColorStateList(new int[][] { new int[] {}}, new int[] { Color.BLUE });
TextAppearanceSpan highlightSpan = new TextAppearanceSpan(null, Typeface.BOLD, -1, blueColor, null);
spannable.setSpan(highlightSpan, startPos, endPos, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);

```

It's creating a highlight span, setting its color as ```blue``` and then setting that as a span of the original ```Spannable``` String using the start and end pos of the query string's occurence in the original string. And the result is a very good highlighted word.


