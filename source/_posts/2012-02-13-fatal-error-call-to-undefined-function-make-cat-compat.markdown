---
layout: post
title: "Fatal error: Call to undefined function _make_cat_compat()"
date: 2011-11-05 13:23
comments: true
categories: [wordpress, php, fix]
description: "How to fix WordPress error Call to undefined function _make_cat_compat()"
keywords: "wordpress, php, error, fix"
---
If you have the following error after uploading your WordPress, and re-uploading `category-template.php` didn't work, you could try pasting this function at the of the file.

``` php
<?php 

function _make_cat_compat( &$category ) {
    if ( is_object( $category ) ) {
        $category->cat_ID = &$category->term_id;
        $category->category_count = &$category->count;
        $category->category_description = &$category->description;
        $category->cat_name = &$category->name;
        $category->category_nicename = &$category->slug;
        $category->category_parent = &$category->parent;
    } 
    elseif ( is_array( $category ) && isset( $category['term_id'] ) ) {
        $category['cat_ID'] = &$category['term_id'];
        $category['category_count'] = &$category['count'];
        $category['category_description'] = &$category['description'];
        $category['cat_name'] = &$category['name'];
        $category['category_nicename'] = &$category['slug'];
        $category['category_parent'] = &$category['parent'];
    }
}

?>
```