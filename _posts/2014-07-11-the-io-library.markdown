---
layout: post
title: "The IO Library"
date: 2014-07-11 09:25:11 +0800
comments: true
category: C++
tags: [IO]
---


>- To support different kinds of IO processing,IO types are defined in three separate headers:iostream defines the basic types used to read from and write to a stream, fstream defines the types used to read and write named files, and sstream defines the types used to read and write in-memory strings.
>- Caution:Buffers Are Not Flushed If the Program Crashes.
>- When an fstream object is destroyed, close is called automatically.
>- The only way to preserve the existing data in a file opened by an ofstream is to specify app or in mode explicitly.
>- The sstream header defines three types to support in-memory IO; these types read from or write to a string as if the string were an IO stream.The *istringstream* type reads a string, *ostringstream* writes a string, and *stringstream* reads and writes the string.An *istringstream* is often used when we have some work to do on an entire line,and other work to do with individual words within a line.An *ostringstream* is useful when we need to build up our output a little at a time but do not want to print the output until later.


    string line, word;  // will hold a line and word from input,respectively
    vector<PersonInfo> people; // will hold all the records from the input
    // read the input a line at a time until cin hits end-of-file (or another error)
    while (getline(cin, line)) {
    	PersonInfo info;  // create an object to hold this record's data
    	istringstream record(line); // bind record to the line we just read
    	record >> info.name;  // read the name
    	while (record >> word)  // read the phone numbers
    	info.phones.push_back(word);  // and store them
    	people.push_back(info); // append this record to people
    }
    
    for(const auto &entry : people) {  // for each entry in people
    	ostringstream formatted, badNums; // objects created on each loop
    	for (const auto &nums : entry.phones) { // for each number
    		if (!valid(nums)) {
    			badNums << " " << nums;  // string in badNums
    } 
    		// ''writes'' to formatted's string
    else formatted << " " << format(nums);
    }
    	if (badNums.str().empty())  // there were no bad numbers
    	   os << entry.name << " "  // print the name
    	   << formatted.str() << endl; // and reformatted numbers
    	else  // otherwise, print the name and bad numbers
      cerr << "input error: " << entry.name
      << " invalid number(s) " << badNums.str() <<endl;
    }  
