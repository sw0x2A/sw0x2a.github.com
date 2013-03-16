---
title: 'Perl: Inserting a lot of documents in MongoDB'
author: sw
layout: post
permalink: /2011/08/perl-inserting-a-lot-of-documents-in-mongodb/
categories:
  - Uncategorized
tags:
  - batch_insert
  - benchmark
  - benchmarking
  - find_one
  - insert
  - mongodb
  - perl
  - profiling
---
# 

In the last few day, I spend a lot of time to figure out and learn how [MongoDB][1] works and what is the best solution to fill a collection with millions of records (documents). To be more precise, I want to store unique strings and some other data related to that strings. One should be able to search for values in all fields, therefore I set indexes for all of them and a unique one for the strings.

 [1]: http://www.mongodb.org/

Since I want to insert a lot of records, I thought it would be a good idea to use `batch_insert` (see [API Documentation for Perl driver][2]), which would insert each document in an array into the database. It turned out very fast that I have to check for duplicate strings because `batch-insert` breaks immediately when it gets an error from the unique index and does not insert the remaining documents. I ended up with something like the following, which does what it should but I had a bad feeling and started to profile that code.

 [2]: http://api.mongodb.org/perl/current/MongoDB/Collection.html

    while() {
    	chomp(my $string = $_);
    	next if defined($mongo_coll->find_one({ plaintext => $string }));
    	push(@documents, { 
    		"plaintext" =>  "$string",
    		"foo"       =>  "bar",
    	});
    	if ( $counter   == $max_inserts ) {
    		mdb_batch_insert(@documents);
    		@hashes=(); $counter=1;
    	}
    }
    mdb_batch_insert(@documents);
    
    sub mdb_batch_insert {
    	my $documents_ref = shift;
    	$mongo_coll->batch_insert($documents_ref);
    	my $err = $mongo_db->last_error();
    	unless ( $err->{ok} eq 1 and not defined($err->{err}) ) {
    		$logger->error(Dumper($err));
    	}
    }
    

For profiling (using [Devel::SmallProf][3]) and benchmarking I inserted the same 100000 documents twice into an empty collection. Doing so, I have got results for inserts without collisions with unique index and no inserts or inserts with 100% collisions.

 [3]: http://search.cpan.org/perldoc?Devel::SmallProf

The results pointed out that a lot of time was wasted with checking for duplicates and errors. I started to play around with other ways to insert data and was surprised to see that the easiest approach is also the fastest: Since MongoDB takes automatically care of duplicates, it is possible to just insert each document separately without checking for errors.

    while () {
    	chomp( my $plaintext = $_ );
    	$mongo_coll->insert({
    		"plaintext" =>  "$string",
    		"foo"       =>  "bar",
    	});
    }
    

I use that code to insert the first 23 million documents. After approximately 10 million inserts, I could see that performance of MongoDB and my script went down. Another problem was that some documents (~5000) were not inserted due to errors I did not checked for. What have I learned? That I have to check for errors and even when it is not a measurable problem in an almost empty database, of course, `insert` is more expensive than `find_one`.

    while () {
    	chomp( my $plaintext = $_ );
    	next if defined( $mongo_coll->find_one( { plaintext => $plaintext } ) );
    	eval {
    		$mongo_coll->insert(
    			{
    		                "plaintext" =>  "$plaintext",
    		                "foo"       =>  "bar",
    			},
    			{ safe => 1 }
    		);
    	};
    	$logger->error($@) if $@;
    }
    

At the end, one could say that it would have been obvious that the final solution is the best. Yes, it is how I would have done it with MySQL. But I wanted to become familiar with it and was wondering whether MongoDB would act similar or not. Now I can be sure.