MARK
===============================================================================
_by Ingo Karkat_
_(original version by Yuheng Xie)_

DESCRIPTION
------------------------------------------------------------------------------

This plugin adds mappings and a :Mark command to highlight several words in
different colors simultaneously, similar to the built-in 'hlsearch'
highlighting of search results and the \* star command. For example, when you
are browsing a big program file, you could highlight multiple identifiers in
parallel. This will make it easier to trace the source code.

This is a continuation of [vimscript #1238](http://www.vim.org/scripts/script.php?script_id=1238) by Yuheng Xie, who doesn't maintain
his original version anymore and recommends switching to this fork. This
plugin offers the following advantages over the original:
- Much faster, all colored words can now be highlighted, no more clashes with
  syntax highlighting (due to use of matchadd()).
- Many bug fixes.
- Jumps behave like the built-in search, including wrap and error messages.
- Like the built-in commands, jumps take an optional [count] to quickly skip
  over some marks.
- Marks can be persisted, and patterns can be added / subtracted from
  mark highlight groups.

### SEE ALSO

- SearchAlternatives.vim ([vimscript #4146](http://www.vim.org/scripts/script.php?script_id=4146)) provides mappings and commands to
  add and subtract alternative branches to the current search pattern.
- SearchHighlighting.vim ([vimscript #4320](http://www.vim.org/scripts/script.php?script_id=4320)) can change the semantics of the
  start command \*, extends it to visual mode (like Mark) and has auto-search
  functionality which instantly highlights the word under the cursor when
  typing or moving around, like in many IDEs.
- MarkMarkup.vim ([vimscript #5777](http://www.vim.org/scripts/script.php?script_id=5777)) extends mark.vim with rendering the
  highlightings as markup directly inside the text: as HTML &lt;span&gt; tags that
  reproduce the mark's colors, or as appended numbers or symbols and a legend
  to look up the mark names. Any markup-based export format can be defined.

### RELATED WORKS

- MultipleSearch ([vimscript #479](http://www.vim.org/scripts/script.php?script_id=479)) can highlight in a single window and in all
  buffers, but still relies on the :syntax highlighting method, which is
  slower and less reliable.
- http://vim.wikia.com/wiki/Highlight_multiple_words offers control over the
  color used by mapping the 1-9 keys on the numeric keypad, persistence, and
  highlights only a single window.
- highlight.vim ([vimscript #1599](http://www.vim.org/scripts/script.php?script_id=1599)) highlights lines or patterns of interest in
  different colors, using mappings that start with CTRL-H and work on cword.
- quickhl.vim ([vimscript #3692](http://www.vim.org/scripts/script.php?script_id=3692)) can also list the matches with colors and in
  addition offers on-the-fly highlighting of the current word (like many IDEs
  do).
- Highlight (http://www.drchip.org/astronaut/vim/index.html#HIGHLIGHT) has
  commands and mappings for highlighting and searching, uses matchadd(), but
  limits the scope of highlightings to the current window.
- TempKeyword ([vimscript #4636](http://www.vim.org/scripts/script.php?script_id=4636)) is a simple plugin that can matchadd() the
  word under the cursor with \\0 - \\9 mappings. (And clear with \\c0 etc.)
- simple\_highlighting ([vimscript #4688](http://www.vim.org/scripts/script.php?script_id=4688)) has commands and mappings to highlight
  8 different slots in all buffers.
- searchmatch ([vimscript #4869](http://www.vim.org/scripts/script.php?script_id=4869)) has commands and mappings for :[1,2,3]match,
  in the current window only.
- highlight-groups.vim ([vimscript #5612](http://www.vim.org/scripts/script.php?script_id=5612)) can do buffer-local as well as
  tab-scoped highlighting via :syntax, and has multiple groups whose
  highlighting is defined in an external CSV file.
- Syntax match ([vimscript #5376](http://www.vim.org/scripts/script.php?script_id=5376)) provides various (color-based) shortcut
  commands for :syntax match, and saves and restores those definitions, for
  text and log files.
- SelX ([vimscript #5875](http://www.vim.org/scripts/script.php?script_id=5875)) provides multiple multi-colored highlights per-tab
  (that can be stored in a session), mappings that mirror the built-in search
  commands, as a special feature automatically displays a Highlight Usage Map.
- hi ([vimscript #5887](http://www.vim.org/scripts/script.php?script_id=5887)) highlights words, sentences or regular expressions
  using many configured colors, and can search; also offers separate windows
  for filtering and configuration editing catered towards log analysis.
- vim-highlight-hero ([vimscript #5922](http://www.vim.org/scripts/script.php?script_id=5922)) can also highlight the current word or
  selection, has some flexibility with regard to whitespace matching, is
  limited to the current window.

USAGE
------------------------------------------------------------------------------

### HIGHLIGHTING

    <Leader>m               Mark the word under the cursor, similar to the star
                            command. The next free highlight group is used.
                            If already on a mark: Clear the mark, like
                            <Leader>n.
    {Visual}<Leader>m       Mark or unmark the visual selection.
    {N}<Leader>m            With {N}, mark the word under the cursor with the
                            named highlight group {N}. When that group is not
                            empty, the word is added as an alternative match, so
                            you can highlight multiple words with the same color.
                            When the word is already contained in the list of
                            alternatives, it is removed.

                            When {N} is greater than the number of defined mark
                            groups, a summary of marks is printed. Active mark
                            groups are prefixed with "*" (or "M*" when there are
                            M pattern alternatives), the default next group with
                            ">", the last used search with "/" (like :Marks
                            does). Input the mark group, accept the default with
                            <CR>, or abort with <Esc> or any other key.
                            This way, when unsure about which number represents
                            which color, just use 99<Leader>n and pick the color
                            interactively!

    {Visual}[N]<Leader>m    Ditto, based on the visual selection.

    [N]<Leader>r            Manually input a regular expression to mark.
    {Visual}[N]<Leader>r    Ditto, based on the visual selection.

                            In accordance with the built-in star command,
                            all these mappings use 'ignorecase', but not
                            'smartcase'.

    <Leader>n               Clear the mark under the cursor.
                            If not on a mark: Disable all marks, similar to
                            :nohlsearch.
                            Note: Marks that span multiple lines are not detected,
                            so the use of <Leader>n on such a mark will
                            unintentionally disable all marks! Use
                            {Visual}<Leader>r or :Mark {pattern} to clear
                            multi-line marks (or pass [N] if you happen to know
                            the group number).
    {N}<Leader>n            Clear the marks represented by highlight group {N}.

    :{N}Mark                Clear the marks represented by highlight group {N}.
    :[N]Mark[!] [/]{pattern}[/]
                            Mark or unmark {pattern}. Unless [N] is given, the
                            next free highlight group is used for marking.
                            With [N], mark {pattern} with the named highlight
                            group [N]. When that group is not empty, the word is
                            added as an alternative match, so you can highlight
                            multiple words with the same color, unless [!] is
                            given; then, {pattern} overrides the existing mark.
                            When the word is already contained in the list of
                            alternatives, it is removed.
                            For implementation reasons, {pattern} cannot use the
                            'smartcase' setting, only 'ignorecase'.
                            Without [/], only literal whole words are matched.
                            :search-args
    :Mark                   Disable all marks, similar to :nohlsearch. Marks
                            will automatically re-enable when a mark is added or
                            removed, or a search for marks is performed.

    :MarkClear              Clear all marks. In contrast to disabling marks, the
                            actual mark information is cleared, the next mark will
                            use the first highlight group. This cannot be undone.

    :[N]Mark[!] /{pattern}/ as [name]
                            Mark or unmark {pattern}, and give it [name].
    :{N}MarkName [name]
                            Give [name] to mark group {N}.
    :MarkName!              Clear names for all mark groups.

### SEARCHING

    [count]*         [count]#
    [count]<Leader>* [count]<Leader>#
    [count]<Leader>/ [count]<Leader>?
                            Use these six keys to jump to the [count]'th next /
                            previous occurrence of a mark.
                            You could also use Vim's / and ? to search, since the
                            mark patterns are (optionally, see configuration)
                            added to the search history, too.

                Cursor over mark                    Cursor not over mark
     ---------------------------------------------------------------------------
      <Leader>* Jump to the next occurrence of      Jump to the next occurrence of
                current mark, and remember it       "last mark".
                as "last mark".

      <Leader>/ Jump to the next occurrence of      Same as left.
                ANY mark.

       *        If <Leader>* is the most recently   Do Vim's original * command.
                used, do a <Leader>*; otherwise
                (<Leader>/ is the most recently
                used), do a <Leader>/.

                            Note: When the cursor is on a mark, the backwards
                            search does not jump to the beginning of the current
                            mark (like the built-in search), but to the previous
                            mark. The entire mark text is treated as one entity.

                            You can use Vim's jumplist to go back to previous
                            mark matches and the position before a mark search.

    If you work with multiple highlight groups and assign special meaning to them
    (e.g. group 1 for notable functions, 2 for variables, 3 for includes), you can
    use the 1-9 keys on the numerical keypad to jump to occurrences of a
    particular highlight group. With the general * and # commands above, you'd
    first need to locate a nearby occurrence of the desired highlight group if
    it's not the last mark used.

    <k1> .. <k9>            Jump to the [count]'th next occurrence of the mark
                            belonging to highlight group 1..9.
    <C-k1> .. <C-k9>        Jump to the [count]'th previous occurrence of the mark
                            belonging to highlight group 1..9.
                            Note that these commands only work in GVIM or if your
                            terminal sends different key codes; sadly, most still
                            don't.
                            https://unix.stackexchange.com/questions/552297/make-gnome-terminal-send-correct-numeric-keypad-keycodes-to-vim
                            The "Num Lock" indicator of your keyboard has
                            to be ON; otherwise, the keypad is used for cursor
                            movement. If the keypad doesn't work for you, you can
                            still remap these mappings to alternatives; see below.
    Alternatively, you can set up mappings to search in a next / previous used
    group, see mark-group-cycle.

    [...]
    After a stop, retriggering the cascaded search in the same buffer and window
    moves to the next used group (you can jump inside the current buffer to choose
    a different starting point first). If you instead switch to another window or
    buffer, the current mark group continues to be searched (to allow you to
    keep searching for the current group in other locations, until those are all
    exhausted too).

### MARK PERSISTENCE

    The marks can be kept and restored across Vim sessions, using the viminfo
    file. For this to work, the "!" flag must be part of the 'viminfo' setting:
        set viminfo^=!  " Save and restore global variables.

    :MarkLoad               Restore the marks from the previous Vim session. All
                            current marks are discarded.
    :MarkLoad {slot}        Restore the marks stored in the named {slot}. All
                            current marks are discarded.

    :MarkSave               Save the currently defined marks (or clear the
                            persisted marks if no marks are currently defined) for
                            use in a future Vim session.
    :MarkSave {slot}        Save the currently defined marks in the named {slot}.
                            If {slot} is all UPPERCASE, the marks are persisted
                            and can be |:MarkLoad|ed in a future Vim session (to
                            persist without closing Vim, use :wviminfo; an
                            already running Vim session can import marks via
                            :rviminfo followed by :MarkLoad).
                            If {slot} contains lowercase letters, you can just
                            recall within the current session. When no marks are
                            currently defined, the {slot} is cleared.

    By default, automatic persistence is enabled (so you don't need to explicitly
    :MarkSave), but you have to explicitly load the persisted marks in a new Vim
    session via :MarkLoad, to avoid that you accidentally drag along outdated
    highlightings from Vim session to session, and be surprised by the arbitrary
    highlight groups and occasional appearance of forgotten marks. If you want
    just that though and automatically restore any marks, set g:mwAutoLoadMarks.

    You can also initialize some marks (even using particular highlight groups) to
    static values, e.g. by including this in vimrc:
        runtime plugin/mark.vim
        silent MarkClear
        silent 5Mark foo
        silent 6Mark /bar/
    Or you can define custom commands that preset certain marks:
        command -bar MyMarks exe '5Mark! foo' | exe '6Mark! /bar/'
    Or a command that adds to the existing marks and then toggles them:
        command -bar ToggleFooBarMarks exe 'Mark foo' | exe 'Mark /bar/'
    The following commands help with setting these up:

    :MarkYankDefinitions [x]
                            Place definitions for all current marks into the
                            default register / [x], like this:
                                1Mark! /\<foo\>/
                                2Mark! /bar/
                                9Mark! /quux/
    :MarkYankDefinitionsOneLiner [x]
                            Like :MarkYankDefinitions, but place all definitions
                            into a single line, like this:
                            exe '1Mark! /\<foo\>/' | exe '2Mark! /bar/' | exe '9Mark! /quux/'
    Alternatively, the mark#GetDefinitionCommands(isOneLiner) function can be used
    to obtain a List of :Mark commands instead of using a register. With that,
    you could for example build a custom alternative to :MarkSave that stores
    Marks in separate files (using writefile(), read by :source or even
    automatically via a local vimrc plugin) instead of the viminfo file.

### MARK INFORMATION

    Both mark-highlighting and mark-searching commands print information about
    the mark and search pattern, e.g.
            mark-1/\<pattern\>
    This is especially useful when you want to add or subtract patterns to a mark
    highlight group via [N].

    :Marks                  List all mark highlight groups and the search patterns
                            defined for them.
                            The group that will be used for the next :Mark or
                            <Leader>m command (with [N]) is shown with a ">".
                            The last mark used for a search (via <Leader>*) is
                            shown with a "/".

### MARK HIGHLIGHTING PALETTES

    The plugin comes with three predefined palettes: original, extended, and
    maximum. You can dynamically toggle between them, e.g. when you need more
    marks or a different set of colors.

    :MarkPalette {palette}  Highlight existing and future marks with the colors
                            defined in {palette}. If the new palette contains less
                            mark groups than the current one, the additional marks
                            are lost.
                            You can use :command-completion for {palette}.

    See g:mwDefaultHighlightingPalette for how to change the default palette,
    and mark-palette-define for how to add your own custom palettes.

INSTALLATION
------------------------------------------------------------------------------

The code is hosted in a Git repo at
    https://github.com/inkarkat/vim-mark
You can use your favorite plugin manager, or "git clone" into a directory used
for Vim packages. Releases are on the "stable" branch, the latest unstable
development snapshot on "master".

This script is also packaged as a vimball. If you have the "gunzip"
decompressor in your PATH, simply edit the \*.vmb.gz package in Vim; otherwise,
decompress the archive first, e.g. using WinZip. Inside Vim, install by
sourcing the vimball or via the :UseVimball command.

    vim mark*.vmb.gz
    :so %

To uninstall, use the :RmVimball command.

### DEPENDENCIES

- Requires Vim 7.1 with matchadd(), or Vim 7.2 or higher.
- Requires the ingo-library.vim plugin ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)), version 1.043 or
  higher.

CONFIGURATION
------------------------------------------------------------------------------

For a permanent configuration, put the following commands into your vimrc.

This plugin defines 6 mark groups:
```
    1: Cyan  2:Green  3:Yellow  4:Red  5:Magenta  6:Blue
```
Higher numbers always take precedence and are displayed above lower ones.

Especially if you use GVIM, you can switch to a richer palette of up to 18
colors:

    let g:mwDefaultHighlightingPalette = 'extended'

Or, if you have both good eyes and display, you can try a palette that defines
27, 58, or even 77 colors, depending on the number of available colors:

    let g:mwDefaultHighlightingPalette = 'maximum'

Note: This only works for built-in palettes and those that you define prior to
running the plugin. If you extend the built-ins after plugin initialization
(mark-palette-define), use :MarkPalette instead.

If you like the additional colors, but don't need that many of them, restrict
their number via:

        let g:mwDefaultHighlightingNum = 9

If none of the default highlightings suits you, define your own colors in your
vimrc file (or anywhere before this plugin is sourced, but after any
:colorscheme), in the following form (where N = 1..):

    highlight MarkWordN ctermbg=Cyan ctermfg=Black guibg=#8CCBEA guifg=Black

You can also use this form to redefine only some of the default highlightings.
If you want to avoid losing the highlightings on :colorscheme commands, you
need to re-apply your highlights on the ColorScheme event, similar to how
this plugin does. Or you define the palette not via :highlight commands, but
use the plugin's infrastructure:

    let g:mwDefaultHighlightingPalette = [
    \   { 'ctermbg':'Cyan', 'ctermfg':'Black', 'guibg':'#8CCBEA', 'guifg':'Black' },
    \   ...
    \]

If you want to switch multiple palettes during runtime, you need to define
them as proper palettes.
a) To add your palette to the existing ones, do this _after_ the default
   palette has been defined (e.g. in ~/.vim/after/plugin/mark.vim):

    if ! exists('g:mwPalettes') " (Optional) guard if the plugin isn't properly installed.
        finish
    endif

    let g:mwPalettes['mypalette'] = [
    \   { 'ctermbg':'Cyan', 'ctermfg':'Black', 'guibg':'#8CCBEA', 'guifg':'Black' },
    \   ...
    \]
    let g:mwPalettes['other'] = [ ... ]

    " Make it the default; you cannot use g:mwDefaultHighlightingPalette
    here, as the Mark plugin has already been initialized:
    MarkPalette mypalette

b) Alternatively, you can completely override all built-in palettes in your
   vimrc:

    let g:mwPalettes = {
    \   'mypalette': [
    \       { 'ctermbg':'Cyan', 'ctermfg':'Black', 'guibg':'#8CCBEA', 'guifg':'Black' },
    \       ...
    \   ]
    \}

    " Make it the default:
    let g:mwDefaultHighlightingPalette = 'mypalette'

The search type highlighting (in the search message) can be changed via:

    highlight link SearchSpecialSearchType MoreMsg

By default, any marked words are also added to the search (/) and input (@)
history; if you don't want that, remove the corresponding symbols from:

    let g:mwHistAdd = '/@'

By default, searches with <Leader>\* and <Leader>/ doesn't change the behavior
of the default n/N movement. To change the search register: 

    let g:mwHistSearch = 1

To enable the automatic restore of marks from a previous Vim session:

    let g:mwAutoLoadMarks = 1

To turn off the automatic persistence of marks across Vim sessions:

    let g:mwAutoSaveMarks = 0

You can still explicitly save marks via :MarkSave.

If you have set 'ignorecase', but want marks to be case-insensitive, you can
override the default behavior of using 'ignorecase' by setting:

        let g:mwIgnoreCase = 0

To exclude some tab pages, windows, or buffers / filetypes from showing mark
highlightings (you can still "blindly" navigate to marks in there with the
corresponding mappings), you can define a List of expressions or Funcrefs that
are evaluated in every window; if one returns 1, the window will not show
marks.

    " Don't mark temp files, Python filetype, and scratch files as defined by
    " a custom function.
    let g:mwExclusionPredicates =
    \   ['expand("%:p") =~# "/tmp"', '&filetype == "python", function('ExcludeScratchFiles')]

By default, tab pages / windows / buffers that have t:nomarks / w:nomarks /
b:nomarks with a true value are excluded. Therefore, to suppress mark
highlighting in a buffer, you can simply

    :let b:nomarks = 1

If the predicate changes after a window has already been visible, you can
update the mark highlighting by either:
- switching tab pages back and forth
- toggling marks on / off (via &lt;Plug&gt;MarkToggle)
- :call mark#UpdateMark() (for current buffer)
- :call mark#UpdateScope() (for all windows in the current tab page)

This plugin uses matchadd() for the highlightings. Each mark group has its
own priority, with higher group values having higher priority; i.e. going "on
top". The maximum priority (used for the last defined mark group) can be
changed via:

    let g:mwMaxMatchPriority = -10

For example when another plugin or customization also uses matches and you
would like to change their relative priorities. The default is negative to
step back behind the default search highlighting.

If you want no or only a few of the available mappings, you can completely
turn off the creation of the default mappings by defining:

    :let g:mw_no_mappings = 1

This saves you from mapping dummy keys to all unwanted mapping targets.

You can use different mappings by mapping to the &lt;Plug&gt;Mark... mappings (use
":map &lt;Plug&gt;Mark" to list them all) before this plugin is sourced.

There are no default mappings for toggling all marks and for the :MarkClear
command, but you can define some yourself:

    nmap <Leader>M <Plug>MarkToggle
    nmap <Leader>N <Plug>MarkAllClear

As the latter is irreversible, there's also an alternative with an additional
confirmation:

    nmap <Leader>N <Plug>MarkConfirmAllClear

To remove the default overriding of \* and #, use:

    nmap <Plug>IgnoreMarkSearchNext <Plug>MarkSearchNext
    nmap <Plug>IgnoreMarkSearchPrev <Plug>MarkSearchPrev

If you don't want the \* and # mappings remember the last search type and
instead always search for the next occurrence of the current mark, with a
fallback to Vim's original \* command, use:

    nmap * <Plug>MarkSearchOrCurNext
    nmap # <Plug>MarkSearchOrCurPrev

Or for search for the next occurrence of any mark with fallback to \*:

    nmap * <Plug>MarkSearchOrAnyNext
    nmap # <Plug>MarkSearchOrAnyPrev

Mark searches could also be combined with the built-in search. This mapping
overloads the default n|/|N commands to search for any mark if there is any
mark defined and marks are enabled, and fall back to the default search if
not:

    nmap n <Plug>MarkSearchAnyOrDefaultNext
    nmap N <Plug>MarkSearchAnyOrDefaultPrev

The search mappings (\*, #, etc.) interpret [count] as the number of
occurrences to jump over. If you don't want to use the separate
mark-keypad-searching mappings, and rather want [count] select the highlight
group to target (and you can live with jumps restricted to the very next
match), (re-)define to these mapping targets:

    nmap * <Plug>MarkSearchGroupNext
    nmap # <Plug>MarkSearchGroupPrev

You can remap the direct group searches (by default via the keypad 1-9 keys):

    nmap <Leader>1  <Plug>MarkSearchGroup1Next
    nmap <Leader>!  <Plug>MarkSearchGroup1Prev

If you need more / less groups, this can be configured via:

    let g:mwDirectGroupJumpMappingNum = 20

Set to 0 to completely turn off the keypad mappings. This is easier than
remapping all &lt;Plug&gt;-mappings.

As an alternative to the direct group searches, you can also define mappings
that search a next / previous used group:

    nmap <Leader>+* <Plug>MarkSearchUsedGroupNext
    nmap <Leader>-* <Plug>MarkSearchUsedGroupPrev

Some people like to create a mark based on the visual selection, like
v\_&lt;Leader&gt;m, but have whitespace in the selection match any whitespace when
searching (searching for "hello world" will also find "hello&lt;Tab&gt;world" as
well as "hello" at the end of a line, with "world" at the start of the next
line). The Vim Tips Wiki describes such a setup for the built-in search at
    http://vim.wikia.com/wiki/Search_for_visually_selected_text
You can achieve the same with the Mark plugin through the &lt;Plug&gt;MarkIWhiteSet
mapping target: Using this, you can assign a new visual mode mapping &lt;Leader&gt;\*

    xmap <Leader>* <Plug>MarkIWhiteSet

or override the default v\_&lt;Leader&gt;m mapping, in case you always want this
behavior:

    vmap <Plug>IgnoreMarkSet <Plug>MarkSet
    xmap <Leader>m <Plug>MarkIWhiteSet

INTEGRATION
------------------------------------------------------------------------------

The following functions offer (read-only) access to the script's internals:
- mark#GetGroupNum(): number of available groups
- mark#GetCount(): number of defined marks
- mark#GetPattern([{index}]): search regular expression for an individual mark
- mark#GetMarkNumber({pattern}, {isLiteral}, {isConsiderAlternatives}): mark
  number of a pattern / literal text

LIMITATIONS
------------------------------------------------------------------------------

- If the 'ignorecase' setting is changed, there will be discrepancies between
  the highlighted marks and subsequent jumps to marks.
- If {pattern} in a :Mark command contains atoms that change the semantics of
  the entire (/\\c, /\\C) regular expression, there may be discrepancies
  between the highlighted marks and subsequent jumps to marks.

### CONTRIBUTING

Report any bugs, send patches, or suggest features via the issue tracker at
https://github.com/inkarkat/vim-mark/issues or email (address below).

HISTORY
------------------------------------------------------------------------------

##### 3.2.0   RELEASEME
- Add mark#GetMarkNumber(), based on feedback by Snorch in #36.
- Mark updates across windows now use win\_execute() (since Vim 8.1.1418)
  instead of :windo. This hopefully addresses the changes in window sizes that
  have been reported (e.g. in #34).
- Add &lt;Plug&gt;MarkSearchAnyOrDefaultNext and &lt;Plug&gt;MarkSearchAnyOrDefaultPrev
  for an any-mark search with fallback to the built-in search pattern.
  Suggested by Denis Kasak.

__You need to update to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.043!__

##### 3.1.1   03-Aug-2020
- Compatibility: After Vim 8.1.1241, a :range outside the number of buffers
  (e.g. :99Mark[Name]) causes an error.
- ENH: Add (GUI-only) additional palettes "soft" and "softer" that are
  variants of "extended" with less saturation / higher brightness of
  background colors (for when the default colors are too distracting).
- ENH: Marks that cover multiple lines (created through a visual selection or
  :Mark /{pattern}/) now also can be jumped to when the cursor is not on the
  mark's first line.

__You need to update to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.042!__

##### 3.1.0   23-Mar-2019
- ENH: Handle magicness atoms (\\V, \\m) in regexps entered via &lt;Leader&gt;r or
  :Mark /{pattern}/.
- ENH: Choose a more correct insertion point with multiple alternatives for a
  mark by projecting the length of the existing and alternatives and the added
  pattern.
- BUG: Regression: &lt;Leader&gt;n without {N} and not on an existing mark prints
  error "Do not pass empty pattern to disable all marks".
- ENH: Allow to exclude certain tab pages, windows, or buffers / filetypes
  from showing mark highlightings via g:mwExclusionPredicates or (with the
  default predicate) t:nomarks / w:nomarks / b:nomarks flags.
- ENH: Allow to tweak the maximum match priority via g:mwMaxMatchPriority for
  better coexistence with other customizations that use :match / matchadd().
- ENH: Allow to disable all default mappings via a single g:mw\_no\_mappings
  configuration flag.
- ENH: Appended (strong) green and yellow highlightings to the extended
  palette.
- Refactoring: Move mark persistence implementation to ingo-library. No need
  to serialize into String type for viminfo beyond Vim 7.3.030.
- BUG: Avoid creating jump when updating marks. Need to use :keepjumps windo.
  Reported by epheien.

__You need to update to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.036!__

##### 3.0.0   18-Sep-2017
- CHG: Parse :Mark arguments as either /{pattern}/ or whole {word}. This
  better addresses the common use case of searching for whole words, and is
  consistent with built-in commands like :djump.
- ENH: Keep previous (last accessed) window on :windo.
- Consistently use :noautocmd during window iteration.
- ENH: Add :MarkYankDefinitions and :MarkYankDefinitionsOneLiner commands.
  These make it easier to persist marks for specific files (e.g. by putting
  the :Mark commands into a local vimrc) or occasions (by defining a custom
  command or mapping with these commands), and are an alternative to
  :MarkSave/Load.
- ENH: Add &lt;Plug&gt;MarkSearchUsedGroupNext and &lt;Plug&gt;MarkSearchUsedGroupPrev to
  search in a next / previous used group. Suggested by Louis Pan.
- ENH: Add &lt;Plug&gt;MarkSearchCascadeStartWithStop,
  &lt;Plug&gt;MarkSearchCascadeNextWithStop, &lt;Plug&gt;MarkSearchCascadeStartNoStop,
  &lt;Plug&gt;MarkSearchCascadeNextNoStop to search in cascading mark groups, i.e.
  first all matches for group 1, then all for group 2, and so on.
- CHG: Duplicate mark#GetNum() and mark#GetGroupNum(). Rename the former into
  mark#GetCount() and have it return the number of actually defined (i.e.
  non-empty) marks.
- ENH: Allow to give names to mark groups via :MarkName and new :Mark
  /{pattern}/ as {name} command syntax. Names will be shown during searching,
  and persisted together with the marks. This makes it easier to handle
  several marks and enforce custom semantics for particular groups.
- Properly abort on error by using :echoerr.
- Add dependency to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)).

__You need to separately
  install ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.020 (or higher)!__

##### 2.8.5   29-Oct-2014
- ENH: Add alternative &lt;Plug&gt;MarkConfirmAllClear optional command that works
  like &lt;Plug&gt;MarkAllClear, but with confirmation. Thanks to Marcelo Montu for
  suggesting this!

##### 2.8.4   19-Jun-2014
- To avoid accepting an invalid regular expression (e.g. "\\(blah") and then
  causing ugly errors on every mark update, check the patterns passed by the
  user for validity.
- CHG: The :Mark command doesn't query for a mark when the passed mark group
  doesn't exist (interactivity in Ex commands is unexpected). Instead, it
  returns an error.

##### 2.8.3   23-May-2014
- The additional mapping described under :help mark-whitespace-indifferent got
  broken again by the refactoring of mark#DoMark() on 31-Jan-2013. Finally
  include this in the script as &lt;Plug&gt;MarkIWhiteSet and
  mark#GetVisualSelectionAsLiteralWhitespaceIndifferentPattern(). Thanks to
  Greg Klein for noticing and prodding me to include it.

##### 2.8.2   16-Dec-2013
- BUG: :Mark cannot highlight patterns starting with a number. Use -range=0
  instead of -count. Thanks to Vladimir Marek for reporting this.

##### 2.8.1   22-Nov-2013
- Allow to override the adding to existing marks via :[N]Mark! {pattern}.
- ENH: Implement command completion for :[N]Mark that offers existing mark
  patterns (from group [N] / all groups), both as one regular expression and
  individual alternatives. The leading \\&lt; can be omitted.

##### 2.8.0   01-Jun-2013
- Also allow a [count] for &lt;Leader&gt;r to select (or query for) a mark group, as
  with &lt;Leader&gt;m.
- CHG: Also set the current mark to the used mark group when a mark was set
  via &lt;Leader&gt;r and :Mark so that it is easier to determine whether the
  entered pattern actually matches anywhere. Thanks to Xiaopan Zhang for
  notifying me about this problem.
- Add &lt;Plug&gt;MarkSearchGroupNext / &lt;Plug&gt;MarkSearchGroupPrev to enable
  searching for particular mark groups. Thanks to Xiaopan Zhang for the
  suggestion.
- Define default mappings for keys 1-9 on the numerical keypad to jump to a
  particular group (backwards with &lt;C-kN&gt;). Their definition is controlled by
  the new g:mwDirectGroupJumpMappingNum variable.
- ENH: Allow to store an arbitrary number of marks via named slots that can
  optionally be passed to :MarkLoad / :MarkSave. If the slot is all-uppercase,
  the marks will also be persisted across Vim invocations.

##### 2.7.2   15-Oct-2012
- Issue an error message "No marks defined" instead of moving the cursor by
  one character when there are no marks (e.g. initially or after :MarkClear).
- Enable custom integrations via new mark#GetNum() and mark#GetPattern()
  functions.

##### 2.7.1   14-Sep-2012
- Enable alternative \* / # mappings that do not remember the last search type
  through new &lt;Plug&gt;MarkSearchOrCurNext, &lt;Plug&gt;MarkSearchOrCurPrev,
  &lt;Plug&gt;MarkSearchOrAnyNext, &lt;Plug&gt;MarkSearchOrAnyPrev mappings. Based on an
  inquiry from Kevin Huanpeng Du.

##### 2.7.0   04-Jul-2012
- ENH: Implement :MarkPalette command to switch mark highlighting on-the-fly
  during runtime.
- Add "maximum" palette contributed by rockybalboa4.

##### 2.6.5   24-Jun-2012
- Don't define the default &lt;Leader&gt;m and &lt;Leader&gt;r mappings in select mode,
  just visual mode. Thanks to rockybalboa4 for pointing this out.

##### 2.6.4   23-Apr-2012
- Allow to override 'ignorecase' setting via g:mwIgnoreCase. Thanks to fanhe
  for the idea and sending a patch.

##### 2.6.3   27-Mar-2012
- ENH: Allow choosing of palette and limiting of default mark highlight groups
  via g:mwDefaultHighlightingPalette and g:mwDefaultHighlightingNum.
- ENH: Offer an extended color palette in addition to the original 6-color one.
  Enable this via :let g:mwDefaultHighlightingPalette = "extended" in your
  vimrc.

##### 2.6.2   26-Mar-2012
- ENH: When a [count] exceeding the number of available mark groups is given,
  a summary of marks is given and the user is asked to select a mark group.
  This allows to interactively choose a color via 99&lt;Leader&gt;m.
  If you use the mark-whitespace-indifferent mappings,

__PLEASE UPDATE THE
  vnoremap &lt;Plug&gt;MarkWhitespaceIndifferent DEFINITION__
- ENH: Include count of alternative patterns in :Marks list.
- CHG: Use "&gt;" for next mark and "/" for last search in :Marks.

##### 2.6.1   23-Mar-2012
- ENH: Add :Marks command that prints all mark highlight groups and their
  search patterns, plus information about the current search mark, next mark
  group, and whether marks are disabled.
- ENH: Show which mark group a pattern was set / added / removed / cleared.
- FIX: When the cursor is positioned on the current mark, [N]&lt;Leader&gt;n /
  &lt;Plug&gt;MarkClear with [N] appended the pattern for the current mark (again
  and again) instead of clearing it. Must not pass current mark pattern when
  [N] is given.
- CHG: Show mark group number in same-mark search and rename search types from
  "any-mark", "same-mark", and "new-mark" to the shorter "mark-\*", "mark-N",
  and "mark-N!", respectively.

##### 2.6.0   22-Mar-2012
- ENH: Allow [count] for &lt;Leader&gt;m and :Mark to add / subtract match to / from
  highlight group [count], and use [count]&lt;Leader&gt;n to clear only highlight
  group [count]. This was also requested by Philipp Marek.
- FIX: :Mark and &lt;Leader&gt;n actually toggled marks back on when they were
  already off. Now, they stay off on multiple invocations. Use :call
  mark#Toggle() / &lt;Plug&gt;MarkToggle if you want toggling.

##### 2.5.3   02-Mar-2012
- BUG: Version check mistakenly excluded Vim 7.1 versions that do have the
  matchadd() function. Thanks to Philipp Marek for sending a patch.

##### 2.5.2   09-Nov-2011
- Fixed various problems with wrap-around warnings:
- BUG: With a single match and 'wrapscan' set, a search error was issued.
- FIX: Backwards search with single match leads to wrong error message
  instead.
- FIX: Wrong logic for determining l:isWrapped lets wrap-around go undetected.

##### 2.5.1   17-May-2011
- FIX: == comparison in s:DoMark() leads to wrong regexp (\\A vs. \\a) being
  cleared when 'ignorecase' is set. Use case-sensitive comparison ==# instead.
- Refine :MarkLoad messages
- Add whitespace-indifferent visual mark configuration example. Thanks to Greg
  Klein for the suggestion.

##### 2.5.0   07-May-2011
- ENH: Add explicit mark persistence via :MarkLoad and :MarkSave commands and
  automatic persistence via the g:mwAutoLoadMarks and g:mwAutoSaveMarks
  configuration flags. (Request from Mun Johl, 16-Apr-2010)
- Expose toggling of mark display (keeping the mark patterns) via new
  &lt;Plug&gt;MarkToggle mapping. Offer :MarkClear command as a replacement for the
  old argumentless :Mark command, which now just disables, but not clears all
  marks.

##### 2.4.4   18-Apr-2011
- BUG: Include trailing newline character in check for current mark, so that a
  mark that matches the entire line (e.g. created by V&lt;Leader&gt;m) can be
  cleared via &lt;Leader&gt;n. Thanks to ping for reporting this.
- FIX: On overlapping marks, mark#CurrentMark() returned the lowest, not the
  highest visible mark. So on overlapping marks, the one that was not visible
  at the cursor position was removed; very confusing! Use reverse iteration
  order.
- FIX: To avoid an arbitrary ordering of highlightings when the highlighting
  group names roll over, and to avoid order inconsistencies across different
  windows and tabs, we assign a different priority based on the highlighting
  group.

##### 2.4.3   16-Apr-2011
- Avoid losing the mark highlightings on :syn on or :colorscheme commands.
  Thanks to Zhou YiChao for alerting me to this issue and suggesting a fix.
- Made the script more robust when somehow no highlightings have been defined
  or when the window-local reckoning of match IDs got lost. I had very
  occasionally encountered such script errors in the past.
- Made global housekeeping variables script-local, only g:mwHistAdd is used
  for configuration.

##### 2.4.2   14-Jan-2011 (unreleased)
- FIX: Capturing the visual selection could still clobber the blockwise yank
  mode of the unnamed register.

##### 2.4.1   13-Jan-2011
- FIX: Using a named register for capturing the visual selection on
  {Visual}&lt;Leader&gt;m and {Visual}&lt;Leader&gt;r clobbered the unnamed register. Now
  using the unnamed register.

##### 2.4.0   13-Jul-2010
- ENH: The MarkSearch mappings (&lt;Leader&gt;[\*#/?]) add the original cursor
  position to the jump list, like the built-in [/?\*#nN] commands. This allows
  to use the regular jump commands for mark matches, like with regular search
  matches.

##### 2.3.3   19-Feb-2010
- BUG: Clearing of an accidental zero-width match (e.g. via :Mark \\zs) results
  in endless loop. Thanks to Andy Wokula for the patch.

##### 2.3.2   17-Nov-2009
- BUG: Creation of literal pattern via '\\V' in {Visual}&lt;Leader&gt;m mapping
  collided with individual escaping done in &lt;Leader&gt;m mapping so that an
  escaped '\\\*' would be interpreted as a multi item when both modes are used
  for marking. Thanks to Andy Wokula for the patch.

##### 2.3.1   06-Jul-2009
- Now working correctly when 'smartcase' is set. All mappings and the :Mark
  command use 'ignorecase', but not 'smartcase'.

##### 2.3.0   04-Jul-2009
- All jump commands now take an optional [count], so you can quickly skip over
  some marks, as with the built-in \*/# and n/N commands. For this, the entire
  core search algorithm has been rewritten. The script's logic has been
  simplified through the use of Vim 7 features like Lists.
- Now also printing a Vim-alike search error message when 'nowrapscan' is set.

##### 2.2.0   02-Jul-2009
- Split off functions into autoload script.
- Initialization of global variables and autocommands is now done lazily on
  the first use, not during loading of the plugin. This reduces Vim startup
  time and footprint as long as the functionality isn't yet used.
- Split off documentation into separate help file. Now packaging as VimBall.

##### 2.1.0   06-Jun-2009
- Replaced highlighting via :syntax with matchadd() / matchdelete(). This
  requires Vim 7.2 / 7.1 with patches. This method is faster, there are no
  more clashes with syntax highlighting (:match always has preference), and
  the background highlighting does not disappear under 'cursorline'.
- Using winrestcmd() to fix effects of :windo: By entering a window, its
  height is potentially increased from 0 to 1.
- Handling multiple tabs by calling s:UpdateScope() on the TabEnter event.

##### 2.0.0   01-Jun-2009
- Now using Vim List for g:mwWord and thus requiring Vim 7. g:mwCycle is now
  zero-based, but the syntax groups "MarkWordx" are still one-based.
- Factored :syntax operations out of s:DoMark() and s:UpdateMark() so that
  they can all be done in a single :windo.
- Normal mode &lt;Plug&gt;MarkSet now has the same semantics as its visual mode
  cousin: If the cursor is on an existing mark, the mark is removed.
  Beforehand, one could only remove a visually selected mark via again
  selecting it. Now, one simply can invoke the mapping when on such a mark.

##### 1.6.1   31-May-2009
- Publication of improved version by Ingo Karkat.
- Now prepending search type ("any-mark", "same-mark", "new-mark") for better
  identification.
- Retired the algorithm in s:PrevWord in favor of simply using &lt;cword&gt;, which
  makes mark.vim work like the \* command. At the end of a line, non-keyword
  characters may now be marked; the previous algorithm preferred any preceding
  word.
- BF: If 'iskeyword' contains characters that have a special meaning in a
  regexp (e.g. [.\*]), these are now escaped properly.
- Highlighting can now actually be overridden in the vimrc (anywhere _before_
  sourcing this script) by using ':hi def'.
- Added missing setter for re-inclusion guard.

##### 1.5.0   01-Sep-2008
- Bug fixes and enhancements by Ingo Karkat.
- Added &lt;Plug&gt;MarkAllClear (without a default mapping), which clears all
  marks, even when the cursor is on a mark.
- Added &lt;Plug&gt;... mappings for hard-coded \\\*, \\#, \\/, \\?, \* and #, to allow
  re-mapping and disabling. Beforehand, there were some &lt;Plug&gt;... mappings
  and hard-coded ones; now, everything can be customized.
- BF: Using :autocmd without &lt;bang&gt; to avoid removing _all_ autocmds for the
  BufWinEnter event. (Using a custom :augroup would be even better.)
- BF: Explicitly defining s:current\_mark\_position; some execution paths left
  it undefined, causing errors.
- ENH: Make the match according to the 'ignorecase' setting, like the star
  command.
- ENH: The jumps to the next/prev occurrence now print 'search hit BOTTOM,
  continuing at TOP" and "Pattern not found:..." messages, like the \* and n/N
  Vim search commands.
- ENH: Jumps now open folds if the occurrence is inside a closed fold, just
  like n/N do.

##### 1.1.8-g 25-Apr-2008
- Last version published by Yuheng Xie on vim.org.

##### 1.1.2   22-Mar-2005
- Initial version published by Yuheng Xie on vim.org.

------------------------------------------------------------------------------
Copyright: (C) 2008-2021 Ingo Karkat -
           (C) 2005-2008 Yuheng Xie -
The [VIM LICENSE](http://vimdoc.sourceforge.net/htmldoc/uganda.html#license) applies to this plugin.

Maintainer:     Ingo Karkat &lt;ingo@karkat.de&gt;
