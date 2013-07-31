{{$meta}}
type: guide
title: Backlift analytics
{{$endmeta}}

{{$layout /index.html as content}}

## Brief introduction

Backlift analytics API makes it easy to do A/B testing for your application. The point of A/B testing is to try out different variations of the app and then determine which works the best. For example, we might have different versions of our landing page's headline, and we want to figure out what kind of phrasing gets the most signups. To do this, we randomly divide visitors into different groups who only see one version of the text and then track both visits and signups for each group. Finally, we are able to see which group had most signups when compared to total number of visits, and can start showing that version to all visitors.


## Turning analytics on

By default, Backlift apps **don't** collect metrics. For any of the below work, you must turn analytics on. To do this, add this line to `config.yml`:

	analytics: yes


## Dividing users into groups

For analytics, visitors are divided into groups called *variants*. You can define variants in `config.yml`:

	variants:
		a: 0.4
		b: 0.4
		c: 0.2

This sets up three possible variants, `a`, `b`, and `c`. Number after is each variant describes how visitors are distributed across them. In the example above, 40% of visitors would end up being in variant A, 40% in variant B and 20% in variant C.

Each visitor's variant is consistent across different visits. If a visitor is once assigned into variant "a", it will be the same also for all next visits.

Variant **names can contain only letters and numbers**. Hyphens, spaces, dots or other special characters are not allowed.

## Using template tags to render HTML for variants

You can control what parts of an HTML file is rendered to each variant by using Backlift template tags. In HTML files, you can write:

{{$raw}}

	<p>I'm visible to everyone</p>

	{{$ variant a}}  
		<h1>Only those in variant "a" can see me<h1>
	{{$ endvariant }}

	{{$ variant b}}
		<h1>And I'm seen only by variant "b"
	{{$ endvariant }}

{{$endraw}}

Any HTML inside `{{$ variant variantname }} … {{$ endvariant}}` is shown only those who belong to variant with that name.  

## Analytics

Backlift analytics is based on tracking different events for each variant, which are then summarized for each day. We can for example track count each visit and each signup for all variants, and from that determine which one has best signups / visits -ratio (so called *conversion rate*).

### Different events

There are two different events we track: `visits`and `completions`. Events unique for each visitor for each day, so if someone visits your site twice on Monday, that'll count only as one visit, but when they come back also on Tuesday, it's counted as one more visit

`Visits` are recorded automatically when someone comes to your site. Since we count events only once for each visitor for each event type, `visits` are effectively *daily unique visitors*.

`Completions` are recorded when a visitor successfully completes an action. Backlift detects visitors variant, and automatically attributes completion to a correct one. `Completions` are triggered by two ways:

1. In Javascript by making a `POST` request to `/backlift/analytics/completed`. For example, you might want to trigger a completed-event when a user has scrolled to the bottom of a page:
	
		/* http://stackoverflow.com/questions/3898130/how-to-check-if-a-user-has-scrolled-to-the-bottom */

		// once user is in bottom, do:

		$.post('/backlift/analytics/completed',{}, function(){
			console.log('Counted one completed event')
		});

2. Backlift automatically tracks completed-events whenever you (as a developer) or your user saves some data. This also implicitly triggers a completed event:

		$.post('/backlift/data/todos', {todo: 'Stuff', done: false}, function(){
			console.log('Todo added')
		})


### Reading recorded events

You can read daily event counts by making a `GET` request to `/backlift/analytics/history`. It will return something like this:

	[{
		a: {
			visits: 10,
			completions: 4
		},
		b: {
			visits: 11,
			completions: 8
		},
		_created: '2013-05-23T23:59:59Z'
	},
	{
		a: { … }
		b: { … }
		_created: '2013-05-24T23:59:59Z'
	}]

Basically, it's a list where every item is all data from one day. Keys (besides of _created) are names of variants and each variant has a dictionary of event: count -value pairs. On May 23rd, there were 10 visits by variant "a" and 11 visits by variant "b".

You'll also notice, that each day has a "_created" key, which is timed to be one second before midnight. Data is summed up just before mightnight, so event counts contain all data for that day.

That also means, that latest available data from history API is from yesterday. If you need numbers for current day, you can do `GET` to `/backlift/analytics/current`. Response is otherwise similar to `history`, except that it contains only one item and isn't wrapped inside a list:

	{
		a: {
			visits: 4,
			completions: 3
		},
		b: {
			visits: 4,
			completions: 4
		},
		_created: '2013-05-25T10:32:15Z'
	}

