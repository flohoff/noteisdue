
# Purpose

Notes do not support notification or reopening after time. So this
bot does it.

There are currently these commands supported:

* reopen: <time spec>
  Closes the note on the next run with a message that it will be reopened.
  Reopens the note when the timespec has been reached.
* notify: <time spec>
  Does not do anything when finding the notes comment but notifies
  with
  "User flohoff requested a notification"

# Time spec format

**noteisdue** uses perls **Date::Manip** parser. It understands absolute and
relative timespecs as 

	next month
	3 months
	3 weeks
	24 hours
	2024-01-01

and the like.

# Database

The tool currently uses a postgres database for persisting note IDs and their due timestamp:

	CREATE TABLE public.notes (
	    id integer NOT NULL,
	    added timestamp without time zone DEFAULT now(),
	    noteid integer NOT NULL,
	    due timestamp without time zone NOT NULL,
	    reopened boolean DEFAULT false,
	    commenttimestamp timestamp without time zone NOT NULL
	    action character varying DEFAULT 'reopen'::character varying,
	    username character varying
	);

	ALTER TABLE ONLY public.notes
	    ADD CONSTRAINT unique_notes_id_commenttimestamp UNIQUE (noteid, commenttimestamp);

