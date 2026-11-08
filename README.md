
# Purpose

Notes do not support notification, reopening or autoclosing after time. So this
bot does it.

* It does not react on anonymous users notes
* Everytime it changes its internal state e.g. creates an action or
  cancels it, it will comment on the note

There are currently these commands supported:

* `reopen: <time spec>`
  Closes the note on the next run with a message that it will be reopened.
  Reopens the note when the timespec has been reached.
* `notify: <time spec>`
  Notifies within the note with: "User flohoff requested a notification"
* `autoclose: <time spec>`
  Will automatically close the bug after time.

# Corner cases

- If a note with "reopen" action is reopened by someone else 
  Action should be canceled and a comment beeing written
- If someone comments on an "autoclose" marked note it
  should cancel the autoclose with a comment
- When there is a notification of User A and User B requests a notification
  aswell - What should happen?

# Time spec format

**noteisdue** uses perls **Date::Manip** parser. It understands absolute and
relative timespecs as 

	next month
	tomorrow
	2 hours
	3 months
	3 weeks
	24 hours
	2024-01-01

and the like.

# Database

The tool currently uses a postgres database for persisting note IDs and their due timestamp:

	CREATE TABLE public.noteactions (
	    id integer NOT NULL,
	    added timestamp without time zone DEFAULT now(),
	    noteid integer NOT NULL,
	    due timestamp without time zone NOT NULL,
	    done boolean DEFAULT false,
	    commenttimestamp timestamp without time zone NOT NULL,
	    lastcommenttimestamp timestamp without time zone NOT NULL,
	    action character varying DEFAULT 'reopen'::character varying,
	    username character varying
	);

	ALTER TABLE ONLY public.noteactions
	    ADD CONSTRAINT unique_noteactions_id_commenttimestamp UNIQUE (noteid, commenttimestamp);

# Authorization

As `noteisdue` needs to use OAuth2 on first invocation noteisdue will present the authorization the user
needs to open. After granting the authorization the user needs to copy the uri from the browser back to
noteisdue.

