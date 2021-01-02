The Adventure PHP Framework almost follows well-known Coding Conventions
of Java but only where suitable for PHP and reasonable for the APF
developer team. This page summarizes all conventions currently aligned
amoung the team members.

## Code formatting rules

-   Files MUST be encoded UTF-8 with UNIX line endings.
-   Use an indent of 3 spaces, with no tabs.
-   There MUST NOT be a hard limit on line length; the soft limit MUST
    be 120 characters; lines SHOULD be 80 characters or less.
-   PHP code must always be delimited by the full-form, standard PHP
    tags ("&lt;?php").
-   For files that contain only PHP code, the closing tag ("?&gt;") is
    never permitted. It is not required by PHP.
-   When present, the abstract and final declarations MUST succeed the
    visibility declaration.
-   When present, the static declaration MUST come after the visibility
    declaration.
-   File header MUST include the APF license block.
-   If namespaces are used the first line after the opening "&lt;?php"
    has to be the namespace declaration. The declaration is followed by
    the license header. After a newline, use statements follow directly
    after the license block.
-   There MUST be one space after the control structure keyword (--&gt;
    if ( ...).
-   There MUST NOT be a space after the opening parenthesis.
-   There MUST NOT be a space before the closing parenthesis.
-   There MUST be one space between the closing parenthesis and the
    opening brace (--&gt; if () { ... }).
-   Class declarations have their opening brace on the same line (class
    Foo { ...).
-   Only one class is permitted in each PHP file (important for
    PSR-0/PSR-4 class loading and Composer integration).
-   Every class MUST have a documentation block.
-   Classes that extend other classes or which implement interfaces
    should declare their dependencies on the same line (class Foo
    extends Bar implements Baz {...).
-   Any variables declared in a class must be listed at the top of the
    class, above the declaration of any methods.
-   Giving access to member variables directly by declaring them as
    public is permitted but discouraged in favor of accessor methods
    (set & get).
-   Methods inside classes must always declare their visibility by using
    one of the private, protected, or public modifiers.
-   As with classes, the brace should always have their opening brace on
    the same line.
-   Every function must have a documentation block. The comment block
    has to be directly above the method. No blank lines!
-   Documentation for getter/setter can be omitted since they are
    self-explantory.
-   If a function or method may throw an exception, add a @throws
    documentation entry for all known exception classes.
-   Function arguments should be separated by a single trailing space
    after the comma delimiter.
-   Arguments with default values go to the end of the argument list.
-   When a string is literal (contains no variable substitutions), the
    apostrophe or "single quote" should always be used to delimit the
    string. Double quotes are not allowed due to performance reasons.
-   When a literal string itself contains apostrophes, content
    containing single-quotes must be escaped.
-   Strings must be concatenated using the "." operator. A space must
    always be added before and after the "." operator to improve
    readability.
-   Variable names MUST be declared in $lowerCamelCase (LCC style).
    Variable names may only contain alphanumeric characters. Underscores
    are not permitted.
-   Function names MUST be declared in lowerCamelCase() (LCC style).
    Function and methods names may only contain alphanumeric characters.
    Underscores are not permitted.
-   For object-oriented programming, accessors for instance or static
    variables should always be prefixed with "get" or "set". In case of
    boolean flags, please use "is" or "has" as method prefix.
-   Classes should be given descriptive names an should always begin
    with an uppercase letter (UCC style).
-   Avoid prefixed class names and prefer using namespaces.
-   Constants should always be all-uppercase, with underscores to
    separate words. This applies to class constants as well.
-   Doc blocks should use @package for namespace declaration, @class for
    referring to the class the doc block is created for. @author and
    @version should be there for classes. @version should be multi-line
    with "
    " tags at the end of each version line.
-   In case of structural or semantical changes, methods should be added
    an @author and/or @version tag to ensure traceability.
-   Code changes related to tracker items MUST be commented using the
    issue id (e.g. ID\#4711).
-   Unused or dicontinued functionality MUST be marked with an
    @deprecated tag followed by a hint (e.g. alternative functionality
    to use).
-   Within function documentation blocks, @param tags should be
    separeated by leading and trailing blank lines. Further, please add
    blank line before first documentation tag.
-   Blank lines are to be set as follows:
    -   Maximum blank lines:
        -   In declarations: 2
        -   In code: 2
        -   Before braces: 1
    -   Minimum blank lines:
        -   After namespaces: 1
        -   Before use statement: 1
        -   After use statement: 1
        -   Around class: 1
        -   After class header: 1
        -   Around field: 0
        -   Around method: 1
        -   Around method body: 0
    -   Blank line before return statement is mandatory.
-   Boolean constands "true" and "false" are to be noted in lowercase.
-   Aligning key/value pairs is recommended.
-   Multi-line method calls are recommended for complex calls.
-   Controls statements (e.g. if) MUST have surrounding braces.

## PHPStorm config file

Please find a sample **settings.jar** to import to your PHPStorm
installation under
[Medium:Settings.jar.zip](/Medium:Settings.jar.zip "wikilink"). Please
don't forget tor rename to **settings.jar** before importing.