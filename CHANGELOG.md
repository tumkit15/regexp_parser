## [1.1.0] - 2018-09-17 - [Janosch Müller](mailto:janosch84@gmail.com)

### Added

- Added `Quantifier` methods `#greedy?`, `#possessive?`, `#reluctant?`/`#lazy?`
- Added `Group::Options#option_changes`
  - shows the options enabled or disabled by the given options group
  - as with all other expressions, `#options` shows the overall active options
- Added `Conditional#reference` and `Condition#reference`, indicating the determinative group
- Added `Subexpression#dig`, acts like [`Array#dig`](http://ruby-doc.org/core-2.5.0/Array.html#method-i-dig)

### Fixed

- Fixed parsing of quantified conditional expressions (quantifiers were assigned to the wrong expression)
- Fixed scanning and parsing of forward-referring subexpression calls (e.g. `\g<+1>`)
- `Root` and `Sequence` expressions now support the same constructor signature as all other expressions

## [1.0.0] - 2018-09-01 - [Janosch Müller](mailto:janosch84@gmail.com)

This release includes several breaking changes, mostly to character sets, #map and properties.

### Changed

- Changed handling of sets (a.k.a. character classes or "bracket expressions")
  * see PR [#55](https://github.com/ammar/regexp_parser/pull/55) / issue [#47](https://github.com/ammar/regexp_parser/issues/47) for details
  * sets are now parsed to expression trees like other nestable expressions
  * `#scan` now emits the same tokens as outside sets (no longer `:set, :member`)
  * `CharacterSet#members` has been removed
  * new `Range` and `Intersection` classes represent corresponding syntax features
  * a new `PosixClass` expression class represents e.g. `[[:ascii:]]`
    * `PosixClass` instances behave like `Property` ones, e.g. support `#negative?`
    * `#scan` emits `:(non)posixclass, :<type>` instead of `:set, :char_(non)<type>`
- Changed `Subexpression#map` to act like regular `Enumerable#map`
  * the old behavior is available as `Subexpression#flat_map`
  * e.g. `parse(/[a]/).map(&:to_s) == ["[a]"]`; used to be `["[a]", "a"]`
- Changed expression emissions for some escape sequences
  * `EscapeSequence::Codepoint`, `CodepointList`, `Hex` and `Octal` are now all used
  * they already existed, but were all parsed as `EscapeSequence::Literal`
  * e.g. `\x97` is now `EscapeSequence::Hex` instead of `EscapeSequence::Literal`
- Changed naming of many property tokens (emitted for `\p{...}`)
  * if you work with these tokens, see PR [#56](https://github.com/ammar/regexp_parser/pull/56) for details
  * e.g. `:punct_dash` is now `:dash_punctuation`
- Changed `(?m)` and the likes to emit as `:options_switch` token (@4ade4d1)
  * allows differentiating from group-local `:options`, e.g. `(?m:.)`
- Changed name of `Backreference::..NestLevel` to `..RecursionLevel` (@4184339)
- Changed B`ackreference::Number#number` from `String` to `Integer` (@40a2231)

### Added

- Added support for all previously missing properties (about 250)
- Added `Expression::UnicodeProperty#shortcut` (e.g. returns "m" for `\p{mark}`)
- Added `#char(s)` and `#codepoint(s)` methods to all `EscapeSequence` expressions
- Added `#number`/`#name`/`#recursion_level` to all backref/call expressions (@174bf21)
- Added `#number` and `#number_at_level` to capturing group expressions (@40a2231)

### Fixed

- Fixed Ruby version mapping of some properties
- Fixed scanning of some property spellings, e.g. with dashes
- Fixed some incorrect property alias normalizations
- Fixed scanning of codepoint escapes with 6 digits (e.g. `\u{10FFFF}`)
- Fixed scanning of `\R` and `\X` within sets; they act as literals there

## [0.5.0] - 2018-04-29 - [Janosch Müller](mailto:janosch84@gmail.com)

### Changed

- Changed handling of Ruby versions (PR [#53](https://github.com/ammar/regexp_parser/pull/53))
  * New Ruby versions are now supported by default
  * Some deep-lying APIs have changed, which should not affect most users:
    * `Regexp::Syntax::VERSIONS` is gone
    * Syntax version names have changed from `Regexp::Syntax::Ruby::Vnnn`
      to `Regexp::Syntax::Vn_n_n`
    * Syntax version classes for Ruby versions without regex feature changes
      are no longer predefined and are now only created on demand / lazily
    * `Regexp::Syntax::supported?` returns true for any argument >= 1.8.6

### Fixed

- Fixed some use cases of Expression methods #strfregexp and #to_h (@e738107)

### Added

- Added full signature support to collection methods of Expressions (@aa7c55a)

## [0.4.13] - 2018-04-04 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Added ruby version files for 2.2.10 and 2.3.7

## [0.4.12] - 2018-03-30 - [Janosch Müller](mailto:janosch84@gmail.com)

- Added ruby version files for 2.4.4 and 2.5.1

## [0.4.11] - 2018-03-04 - [Janosch Müller](mailto:janosch84@gmail.com)

- Fixed UnknownSyntaxNameError introduced in v0.4.10 if
  the gems parent dir tree included a 'ruby' dir

## [0.4.10] - 2018-03-04 - [Janosch Müller](mailto:janosch84@gmail.com)

- Added ruby version file for 2.6.0
- Added support for Emoji properties (available in Ruby since 2.5.0)
- Added support for XPosixPunct and Regional_Indicator properties
- Fixed parsing of Unicode 6.0 and 7.0 script properties
- Fixed parsing of the special Assigned property
- Fixed scanning of InCyrillic_Supplement property

## [0.4.9] - 2017-12-25 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Added ruby version file for 2.5.0

## [0.4.8] - 2017-12-18 - [Janosch Müller](mailto:janosch84@gmail.com)

- Added ruby version files for 2.2.9, 2.3.6, and 2.4.3

## [0.4.7] - 2017-10-15 - [Janosch Müller](mailto:janosch84@gmail.com)

- Fixed a thread safety issue (issue #45)
- Some public class methods that were only reliable for
  internal use are now private instance methods (PR #46)
- Improved the usefulness of Expression#options (issue #43) -
  #options and derived methods such as #i?, #m? and #x? are now
  defined for all Expressions that are affected by such flags.
- Fixed scanning of whitespace following (?x) (commit 5c94bd2)
- Fixed a Parser bug where the #number attribute of traditional
  numerical backreferences was not set correctly (commit 851b620)

## [0.4.6] - 2017-09-18 - [Janosch Müller](mailto:janosch84@gmail.com)

- Added Parser support for hex escapes in sets (PR #36)
- Added Parser support for octal escapes (PR #37)
- Added support for cluster types \R and \X (PR #38)
- Added support for more metacontrol notations (PR #39)

## [0.4.5] - 2017-09-17 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to Janosch Müller (https://github.com/janosch-x):
  * Support ruby 2.2.7 (PR #42)
- Added ruby version files for 2.2.8, 2.3.5, and 2.4.2

## [0.4.4] - 2017-07-10 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to Janosch Müller (https://github.com/janosch-x):
  * Add support for new absence operator (PR #33)
- Thanks to Bartek Bułat (https://github.com/barthez):
  * Add support for Ruby 2.3.4 version (PR #40)

## [0.4.3] - 2017-03-24 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Added ruby version file for 2.4.1

## [0.4.2] - 2017-01-10 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to Janosch Müller (https://github.com/janosch-x):
  * Support ruby 2.4 (PR #30)
  * Improve codepoint handling (PR #27)

## [0.4.1] - 2016-11-22 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Updated ruby version file for 2.3.3

## [0.4.0] - 2016-11-20 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Added Syntax.supported? method
- Updated ruby versions for latest releases; 2.1.10, 2.2.6, and 2.3.2

## [0.3.6] - 2016-06-08 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to John Backus (https://github.com/backus):
  * Remove warnings (PR #26)

## [0.3.5] - 2016-05-30 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to John Backus (https://github.com/backus):
  * Fix parsing of /\xFF/n (hex:escape) (PR #24)

## [0.3.4] - 2016-05-25 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to John Backus (https://github.com/backus):
  * Fix warnings (PR #19)
- Thanks to Dana Scheider (https://github.com/danascheider):
  * Correct error in README (PR #20)
- Fixed mistyped \h and \H character types (issue #21)
- Added ancestry syntax files for latest rubies (issue #22)

## [0.3.3] - 2016-04-26 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Thanks to John Backus (https://github.com/backus):
  * Fixed scanning of zero length comments (PR #12)
  * Fixed missing escape:codepoint_list syntax token (PR #14)
  * Fixed to_s for modified interval quantifiers (PR #17)
- Added a note about MRI implementation quirks to Scanner section

## [0.3.2] - 2016-01-01 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Updated ruby versions for latest releases; 2.1.8, 2.2.4, and 2.3.0
- Fixed class name for UnknownSyntaxNameError exception
- Added UnicodeBlocks support to the parser.
- Added UnicodeBlocks support to the scanner.
- Added expand_members method to CharacterSet, returns traditional
  or unicode property forms of shothands (\d, \W, \s, etc.)
- Improved meaning and output of %t and %T in strfregexp.
- Added syntax versions for ruby 2.1.4 and 2.1.5 and updated
  latest 2.1 version.
- Added to_h methods to Expression, Subexpression, and Quantifier.
- Added traversal methods; traverse, each_expression, and map.
- Added token/type test methods; type?, is?, and one_of?
- Added printing method strfregexp, inspired by strftime.
- Added scanning and parsing of free spacing (x mode) expressions.
- Improved handling of inline options (?mixdau:...)
- Added conditional expressions. Ruby 2.0.
- Added keep (\K) markers. Ruby 2.0.
- Added d, a, and u options. Ruby 2.0.
- Added missing meta sequences to the parser. They were supported by the scanner only.
- Renamed Lexer's method to lex, added an alias to the old name (scan)
- Use #map instead of #each to run the block in Lexer.lex.
- Replaced VERSION.yml file with a constant.
- Updated README
- Update tokens and scanner with new additions in Unicode 7.0.

## [0.1.6] - 2014-10-06 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Fixed test and gem building rake tasks and extracted the gem
  specification from the Rakefile into a .gemspec file.
- Added syntax files for missing ruby 2.x versions. These do not add
  extra syntax support, they just make the gem work with the newer
  ruby versions.
- Added .travis.yml to project root.
- README:
  - Removed note purporting runtime support for ruby 1.8.6.
  - Added a section identifying the main unsupported syntax features.
  - Added sections for Testing and Building
  - Added badges for gem version, Travis CI, and code climate.
- Updated README, fixing broken examples, and converting it from a rdoc file to Github's flavor of Markdown.
- Fixed a parser bug where an alternation sequence that contained nested expressions was incorrectly being appended to the parent expression when the nesting was exited. e.g. in /a|(b)c/, c was appended to the root.

- Fixed a bug where character types were not being correctly scanned within character sets. e.g. in [\d], two tokens were scanned; one for the backslash '\' and one for the 'd'

## [0.1.5] - 2014-01-14 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Correct ChangeLog.
- Added syntax stubs for ruby versions 2.0 and 2.1
- Added clone methods for deep copying expressions.
- Added optional format argument for to_s on expressions to return the text of the expression with (:full, the default) or without (:base) its quantifier.
- Renamed the :beginning_of_line and :end_of_line tokens to :bol and :eol.
- Fixed a bug where alternations with more than two alternatives and one of them ending in a group were being incorrectly nested.
- Improved EOF handling in general and especially from sequences like hex and control escapes.
- Fixed a bug where named groups with an empty name would return a blank token [].
- Fixed a bug where member of a parent set where being added to its last subset.
- Various code cleanups in scanner.rl
- Fixed a few mutable string bugs by calling dup on the originals.
- Made ruby 1.8.6 the base for all 1.8 syntax, and the 1.8 name a pointer to the latest (1.8.7 at this time)
- Removed look-behind assertions (positive and negative) from 1.8 syntax
- Added control (\cc and \C-c) and meta (\M-c) escapes to 1.8 syntax
- The default syntax is now the one of the running ruby version in both the lexer and the parser.

## [0.1.0] - 2010-11-21 - [Ammar Ali](mailto:ammarabuali@gmail.com)

- Initial release
