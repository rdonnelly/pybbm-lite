Settings
========

Basic settings
--------------

PYBB_TOPIC_PAGE_SIZE
....................

Number of posts in topic page (default 10)

PYBB_FORUM_PAGE_SIZE
....................

Number of topics in forum page (default 10)

PYBB_FREEZE_FIRST_POST
......................

Freeze first post in topics (show on every page)

PYBB_DEFAULT_TITLE
..................

Default title for forum index page

PYBB_DEFAULT_AUTOSUBSCRIBE
..........................

Users will be automatically subscribed to topic when create post in it.

PYBB_USE_DJANGO_MAILER
......................

When True, then for sending email pybbm will use django-mailer app. With this app you can
manage emails from your site in queue. But in this case you have to periodically actually
send emails from queue. For more information see `app home page <https://github.com/pinax/django-mailer/>`_.
(default False)



User profile settings
---------------------

Next settings used only if you don't customize user profile model,
user profile creation form or templates.

PYBB_DEFAULT_TIME_ZONE
......................

Default time zone for forum as integer. E.g. setting to 1 means GMT+1 zone. (default +3 Moscow)

Style
-----

You can use builtin templates with custom basic template.

PYBB_TEMPLATE
.............

Builtin templates will inherit this template (default "base.html")


Markup engines
--------------

PYBB_MARKUP
...........

Markup engine used in forum (default 'bbcode')
See PYBB_MARKUP_ENGINES below

PYBB_MARKUP_ENGINES
...................

Dict with avaiable markup engines. One of them should be selected with PYBB_DEFAULT_MARKUP

Markup engine it's a function, that accept post.body as first argument, and return
output as rendered html.

by default PyBBM support `bbcode` and `markdown` markup::

    {
        'bbcode': lambda str: urlize(render_bbcode(str, exclude_tags=['size', 'center'])),
        'markdown': lambda str: urlize(Markdown(safe_mode='escape').convert(str))
    })

Please note, that `size` and `center` tags are disabled by default, enable them if you have right markup for them.

PYBB_QUOTE_ENGINES
..................

Dict with quoting function for every markup engines in PYBB_MARKUP_ENGINES

default is::

    {
        'bbcode': lambda text, username="": '[quote="%s"]%s[/quote]\n' % (username, text),
        'markdown': lambda text, username="": '>'+text.replace('\n','\n>').replace('\r','\n>') + '\n'
    }

Post cleaning/validation
------------------------

PYBB_BODY_CLEANERS
..................

List of 'cleaner' functions for body post to automatically remove undesirable content from posts.
Cleaners are user-aware, so you can disable them for some types of users.

Each function in list should accept `auth.User` instance as first argument and `string` instance as second, returned value will be sended to next function on list or saved and rendered as post body.

for example this is enabled by default `rstrip_str` cleaner::

    def rstrip_str(user, str):
        if user.is_staff:
            return str
        return '\n'.join([s.rstrip() for s in str.splitlines()])

default is::

    [
        pybb.util.rstrip_str, #Replace strings with spaces (tabs, etc..) only with newlines
        pybb.util.filter_blanks, # Replace more than 3 blank lines with only 1 blank line
    ]

PYBB_BODY_VALIDATOR
...................

Extra form validation for body of post.

Called as::

    PYBB_BODY_VALIDATOR(user, body)

at `clean_body` method of `PostForm` Here you can do various checks based on user stats. E.g. allow moderators to post links and don't allow others. By raising::

    forms.ValidationError('Here Error Message')

You can show user what is going wrong during validation.

You can use it for example for time limit between posts, preventing URLs, ...

default is None

Anonymous/guest posting
-----------------------

PYBB_ENABLE_ANONYMOUS_POST
..........................

Allow post for not-authenticated users. False by default.
See :doc:`anonymous posting</anonymous>` for details.

PYBB_ANONYMOUS_USERNAME
.......................

Username for anonymous posts. If no user with this username exists it will be created on first anonymous post.

Premoderation
-------------

PYBB_PREMODERATION
..................

Filter for messages that require pre-moderation. See :doc:`Pre-moderation</premoderation>` for details.

Attachments
-----------

PYBB_ATTACHMENT_ENABLE
......................

Enable attahcments for all users. `False` by default.

PYBB_ATTACHMENT_SIZE_LIMIT
..........................

Maximum attachment limit (in bytes), `1024*1024` (1MB) by default.

PYBB_ATTACHMENT_UPLOAD_TO
.........................

Directory in your media path for uploaded attacments. `pybb_upload/attachments` by default.

Polls
-----

PYBB_POLL_MAX_ANSWERS
.....................

Max count of answers, that user can add to topic. 10 by default.

Permissions
-----------

PYBB_AUTO_USER_PERMISSIONS
..........................

Automatically adds add post and add topic permissions to users on user.save().

PYBB_PERMISSION_HANDLER
.......................

If you need custom permissions (for example, private forums based on application-specific
user groups), you can set `PYBB_PERMISSION_HANDLER` to a class which inherits from
`pybb.permissions.DefaultPermissionHandler`, and override any of the `filter_*` and
`may_*` method. For details, look at the source of `pybb.permissions.DefaultPermissionHandler`.
