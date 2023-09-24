
# Purpose

This tool automatically closes notes of selected users in a selected
bounding box when they add a:

	reopen: <time spec>

to their OpenStreetmap Note and will later reopen them once
the due time has expired.

It needs to run regularly from cron

# Time spec format

**noteisdue** uses perls **Date::Manip** parser. It understands absolute and
relative timespecs as 

	next month
	3 months
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
	);

	ALTER TABLE ONLY public.notes
	    ADD CONSTRAINT unique_notes_id_commenttimestamp UNIQUE (noteid, commenttimestamp);

