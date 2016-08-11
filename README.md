# NAME

Catalyst::ResponseHelpers - Concise response constructors for Catalyst controllers

# SYNOPSIS

    use Catalyst::ResponseHelpers qw< :helpers :status >;

    sub show_user : Chained('/') PathPart('user') Args(1) {
        my ($c, $id) = @_;
        my $user = load_user($id)
            or return NotFound($c, "The user id <$id> couldn't be found.");
        ...
    }

# DESCRIPTION

Various helper functions for setting up the current [Catalyst::Response](https://metacpan.org/pod/Catalyst::Response)
object.  All response helpers call `Catalyst/detach` to stop request
processing.  For clarity in your controller actions, it is nevertheless
recommended that you call these helpers as values to [return()](https://metacpan.org/pod/perlfunc#return).

# EXPORTS

By default, only the helper methods documented below are exported.  You may
explicitly request them using the `:helpers` tag.

You may also request `:status`, which re-exports the `:constants` from
[HTTP::Status](https://metacpan.org/pod/HTTP::Status) into your package.  This is useful for custom status codes.

# FUNCTIONS

## ReturnWithMsg($c, $mid)

Redirects to the request’s `return` parameter, or `/` if no such parameter
exists or if the given URI appears to be external to the app.  The given
`$mid` is set as a query parameter, and should be the result of a
`$c->set_status_msg` or `$c->set_error_msg` call.  These context
methods are normally provided by [Catalyst::Plugin::StatusMessage](https://metacpan.org/pod/Catalyst::Plugin::StatusMessage).

## Redirect($c, $action\_or\_action\_path, @args?)

Passes arguments to ["uri\_for\_action" in Catalyst](https://metacpan.org/pod/Catalyst#uri_for_action) and redirects to the returned
URL.

## RedirectToUrl($c, $url, $status?)

Redirects to the given URL, with an optional custom status.  Status defaults to
302 (HTTP\_FOUND).

## Ok($c, $status?, $msg?)

Sets a body-less 204 No Content response by default, switching to a 200 OK with
a body via ["TextPlain"](#textplain) iff a message is provided.  Both the status and
message may be omitted or provided.  If the message is omitted, a body-less
response is set.

Note that if you're using [Catalyst::Action::RenderView](https://metacpan.org/pod/Catalyst::Action::RenderView) and you specify a
status other than 204 but don't provide a message (e.g. `Ok($c, 200)`),
RenderView will intercept the response and try to render a template.  This
probably isn't what you wanted.  A workaround is to use the proper status code
when sending no content (204) or specify a message (the empty string is OK).

## Forbidden($c, $msg?)

Sets a plain text 403 Forbidden response, with an optional custom message.

## NotFound($c, $msg?)

Sets a plain text 404 Not Found response, with an optional custom message.

## ClientError($c, $status?, $msg?)

Sets a plain text 400 Bad Request response by default, with an optional
custom message.  Both the status and message may be omitted or provided.

## ServerError($c, $status?, $msg?)

Sets a plain text 500 Internal Server Error response by default, with an
optional custom message.  Both the status and message may be omitted or
provided.  The error is logged via ["log" in Catalyst](https://metacpan.org/pod/Catalyst#log).

## TextPlain($c, $status?, $msg?)

Sets a plain text 200 OK response by default, with an optional custom
message.  Both the status and message may be omitted or provided.

## AsJSON($c, $status?, $data)

Sets a JSON 200 OK response by default, with an optional custom status.  Data
should be serializable by a view named `JSON` provided by your application
(e.g. via [Catalyst::View::JSON](https://metacpan.org/pod/Catalyst::View::JSON)).

## FromFile($c, $filename, $mime\_type, $headers?)

Sets a response from the contents of the filename using the specified MIME
type.  `Content-Length` and `Last-Modified` are set from the file.

The `Content-Disposition` is set to `attachment` by default, usually forcing
a download.

An optional arrayref of additional headers may also be provided, which is
passed through to ["FromHandle"](#fromhandle).

## FromCharString($c, $string, $mime\_type, $headers?)

Sets a response from the contents of a **character** string using the specified
MIME type.  The character string will be encoded as UTF-8 bytes.

The `Content-Disposition` is set to `attachment` by default, usually forcing
a download.

An optional arrayref of additional headers may also be provided, which is
passed through to ["FromHandle"](#fromhandle).

## FromHandle($c, $handle, $mime\_type, $headers?)

Sets a response from the contents of the filehandle using the specified MIME
type.  An optional arrayref of additional headers may also be provided, which
is passed to [the response’s](https://metacpan.org/pod/Catalyst::Response) [HTTP::Headers](https://metacpan.org/pod/HTTP::Headers) object.

The `Content-Disposition` is set to `attachment` by default, usually forcing
a download.

# AUTHOR

Thomas Sibley <trsibley@uw.edu>

# THANKS

Inspired in part by seeing John Napiorkowski’s (jnap)
[experimental response helpers in CatalystX::Example::Todo](https://github.com/jjn1056/CatalystX-Example-Todo/blob/master/lib/Catalyst/ResponseHelpers.pm).

# COPYRIGHT

Copyright 2015- by the University of Washington

# LICENSE

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.
