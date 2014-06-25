---
layout: post
title: "Using PHP to parse git history"
date: 2011-05-08 13:19
comments: true
categories: [git, php]
description: "Using PHP to parse git history"
keywords: "git, php, parse, history"
---
Here is how you can store git command output in a good manner after which you will be able to easily retrieve necessary information. This snippet belongs to the project I'm currently working on. 

``` php
<?php
// Author: Ngo Minh Nam

$dir = "/path/to/your/repo/";
$output = array();
chdir($dir);
exec("git log",$output);
$history = array();
foreach($output as $line){
    if(strpos($line, 'commit')===0){
        if(!empty($commit)){
            array_push($history, $commit);  
            unset($commit);
        }
        $commit['hash']   = substr($line, strlen('commit'));
    }
    else if(strpos($line, 'Author')===0){
        $commit['author'] = substr($line, strlen('Author:'));
    }
    else if(strpos($line, 'Date')===0){
        $commit['date']   = substr($line, strlen('Date:'));
    }
    else{       
        $commit['message']  .= $line;
    }
}

print_r($history);

?>
```
