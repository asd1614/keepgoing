### Chapter 3. Lexical Structure

This chapter specifies the lexical structure of the Java programming language.

Programs are written in Unicode (§3.1), but lexical translations are provided (§3.2) so that Unicode escapes (§3.3) can be used to include any Unicode character using only ASCII characters. Line terminators are defined (§3.4) to support the different conventions of existing host systems while maintaining consistent line numbers.

The Unicode characters resulting from the lexical translations are reduced to a sequence of input elements (§3.5), which are white space (§3.6), comments (§3.7), and tokens. The tokens are the identifiers (§3.8), keywords (§3.9), literals (§3.10), separators (§3.11), and operators (§3.12) of the syntactic grammar.

#### 3.1. Unicode
Programs are written using the Unicode character set. Information about this character set and its associated character encodings may be found at <http://www.unicode.org/>.

The Java SE platform tracks the Unicode Standard as it evolves. The precise version of Unicode used by a given release is specified in the documentation of the class Character.

Versions of the Java programming language prior to JDK 1.1 used Unicode 1.1.5. Upgrades to newer versions of the Unicode Standard occurred in JDK 1.1 (to Unicode 2.0), JDK 1.1.7 (to Unicode 2.1), Java SE 1.4 (to Unicode 3.0), Java SE 5.0 (to Unicode 4.0), Java SE 7 (to Unicode 6.0), and Java SE 8 (to Unicode 6.2).

**The Unicode standard was originally designed as a fixed-width 16-bit character encoding. It has since been changed to allow for characters whose representation requires more than 16 bits. The range of legal code points is now U+0000 to U+10FFFF, using the hexadecimal U+n notation. Characters whose code points are greater than U+FFFF are called supplementary characters. To represent the complete range of characters using only 16-bit units, the Unicode standard defines an encoding called UTF-16. In this encoding, supplementary characters are represented as pairs of 16-bit code units, the first from the high-surrogates range, (U+D800 to U+DBFF), the second from the low-surrogates range (U+DC00 to U+DFFF). For characters in the range U+0000 to U+FFFF, the values of code points and UTF-16 code units are the same.**

**The Java programming language represents text in sequences of 16-bit code units, using the UTF-16 encoding.**

Some APIs of the Java SE platform, primarily in the Character class, use 32-bit integers to represent code points as individual entities. The Java SE platform provides methods to convert between 16-bit and 32-bit representations.

This specification uses the terms code point and UTF-16 code unit where the representation is relevant, and the generic term character where the representation is irrelevant to the discussion.

Except for comments (§3.7), identifiers, and the contents of character and string literals (§3.10.4, §3.10.5), all input elements (§3.5) in a program are formed only from ASCII characters (or Unicode escapes (§3.3) which result in ASCII characters).

_ASCII (ANSI X3.4) is the American Standard Code for Information Interchange. The first 128 characters of the Unicode UTF-16 encoding are the ASCII characters_.

#### 3.2. Lexical Translations

A raw Unicode character stream is translated into a sequence of tokens, using the following three lexical translation steps, which are applied in turn:

1. A translation of Unicode escapes (§3.3) in the raw stream of Unicode characters to the corresponding Unicode character. A Unicode escape of the form \uxxxx, where xxxx is a hexadecimal value, represents the UTF-16 code unit whose encoding is xxxx. This translation step allows any program to be expressed using only ASCII characters.

2. A translation of the Unicode stream resulting from step 1 into a stream of input characters and line terminators (§3.4).

3. A translation of the stream of input characters and line terminators resulting from step 2 into a sequence of input elements (§3.5) which, after white space (§3.6) and comments (§3.7) are discarded, comprise the tokens (§3.5) that are the terminal symbols of the syntactic grammar (§2.3).

The longest possible translation is used at each step, even if the result does not ultimately make a correct program while another lexical translation would. There is one exception: if lexical translation occurs in a type context (§4.11) and the input stream has two or more consecutive > characters that are followed by a non-> character, then each > character must be translated to the token for the numerical comparison operator >.

>*The input characters a--b are tokenized (§3.5) as a, --, b, which is not part of any grammatically correct program, even though the tokenization a, -, -, b could be part of a grammatically correct program.*
>
>*Without the rule for > characters, two consecutive > brackets in a type such as List<List<String>> would be tokenized as the signed right shift operator >>, while three consecutive > brackets in a type such as List<List<List<String>>> would be tokenized as the unsigned right shift operator >>>. Worse, the tokenization of four or more consecutive > brackets in a type such as List<List<List<List<String>>>> would be ambiguous, as various combinations of >, >>, and >>> tokens could represent the >>>> characters.*

#### 3.3. Unicode Escapes
A compiler for the Java programming language ("Java compiler") first recognizes Unicode escapes in its input, translating the ASCII characters \u followed by four hexadecimal digits to the UTF-16 code unit (§3.1) for the indicated hexadecimal value, and passing all other characters unchanged. Representing supplementary characters requires two consecutive Unicode escapes. This translation step results in a sequence of Unicode input characters.

```java
UnicodeInputCharacter:
UnicodeEscape
RawInputCharacter

UnicodeEscape:
\ UnicodeMarker HexDigit HexDigit HexDigit HexDigit

UnicodeMarker:
u {u}

HexDigit:
(one of)
0 1 2 3 4 5 6 7 8 9 a b c d e f A B C D E F

RawInputCharacter:
any Unicode character
```

_The \, u, and hexadecimal digits here are all ASCII characters._

In addition to the processing implied by the grammar, for each raw input character that is a backslash \, input processing must consider how many other \ characters contiguously precede it, separating it from a non-\ character or the start of the input stream. If this number is even, then the \ is eligible to begin a Unicode escape; if the number is odd, then the \ is not eligible to begin a Unicode escape.

```
For example, the raw input "\\u2122=\u2122" results in the eleven characters " \ \ u 2 1 2 2 = ™ " (\u2122 is the Unicode encoding of the character ™).
```

If an eligible \ is not followed by u, then it is treated as a RawInputCharacter and remains part of the escaped Unicode stream.

**If an eligible \ is followed by u, or more than one u, and the last u is not followed by four hexadecimal digits, then a compile-time error occurs.**

The character produced by a Unicode escape does not participate in further Unicode escapes.

```
For example, the raw input \u005cu005a results in the six characters \ u 0 0 5 a, because 005c is the Unicode value for \. It does not result in the character Z, which is Unicode character 005a, because the \ that resulted from the \u005c is not interpreted as the start of a further Unicode escape.
```

The Java programming language specifies a standard way of transforming a program written in Unicode into ASCII that changes a program into a form that can be processed by ASCII-based tools. The transformation involves converting any Unicode escapes in the source text of the program to ASCII by adding an extra u - for example, \uxxxx becomes \uuxxxx - while simultaneously converting non-ASCII characters in the source text to Unicode escapes containing a single u each.

This transformed version is equally acceptable to a Java compiler and represents the exact same program. The exact Unicode source can later be restored from this ASCII form by converting each escape sequence where multiple u's are present to a sequence of Unicode characters with one fewer u, while simultaneously converting each escape sequence with a single u to the corresponding single Unicode character.

*A Java compiler should use the \uxxxx notation as an output format to display Unicode characters when a suitable font is not available.*

#### 3.4. Line Terminators
A Java compiler next divides the sequence of Unicode input characters into lines by recognizing line terminators.

```java
LineTerminator:
the ASCII LF character, also known as "newline"
the ASCII CR character, also known as "return"
the ASCII CR character followed by the ASCII LF character
InputCharacter:
UnicodeInputCharacter but not CR or LF
```

Lines are terminated by the ASCII characters CR, or LF, or CR LF. The two characters CR immediately followed by LF are counted as one line terminator, not two.

A line terminator specifies the termination of the // form of a comment (§3.7).

*The lines defined by line terminators may determine the line numbers produced by a Java compiler.*

The result is a sequence of line terminators and input characters, which are the terminal symbols for the third step in the tokenization process.

#### 3.5. Input Elements and Tokens

The input characters and line terminators that result from escape processing (§3.3) and then input line recognition (§3.4) are reduced to a sequence of input elements.

```java
Input:
    {InputElement} [Sub]

InputElement:
    WhiteSpace
    Comment
    Token

Token:
    Identifier
    Keyword
    Literal
    Separator
    Operator

Sub:
    the ASCII SUB character, also known as "control-Z"
```

Those input elements that are not white space or comments are tokens. The tokens are the terminal symbols of the syntactic grammar (§2.3).

White space (§3.6) and comments (§3.7) can serve to separate tokens that, if adjacent, might be tokenized in another manner. For example, the ASCII characters - and = in the input can form the operator token -= (§3.12) only if there is no intervening white space or comment.

As a special concession for compatibility with certain operating systems, the ASCII SUB character (\u001a, or control-Z) is ignored if it is the last character in the escaped input stream.

Consider two tokens x and y in the resulting input stream. If x precedes y, then we say that x is to the left of y and that y is to the right of x.

>For example, in this simple piece of code:
>```java
>class Empty {
>}
>```
>we say that the } token is to the right of the { token, even though it appears, in this two-dimensional representation, downward and to the left of the { token. This convention about the use of the words left and right allows us to speak, for example, of the right-hand operand of a binary operator or of the left-hand side of an assignment.

#### 3.6. White Space

White space is defined as the ASCII space character, horizontal tab character, form feed character, and line terminator characters (§3.4).

```java
WhiteSpace:
    the ASCII SP character, also known as "space"
    the ASCII HT character, also known as "horizontal tab"
    the ASCII FF character, also known as "form feed"
    LineTerminator

```

#### 3.7. Comments

There are two kinds of comments:

* /* text */

    A traditional comment: all the text from the ASCII characters /* to the ASCII characters */ is ignored (as in C and C++).

* // text

    An end-of-line comment: all the text from the ASCII characters // to the end of the line is ignored (as in C++).

```
Comment:
    TraditionalComment
    EndOfLineComment

TraditionalComment:
    / * CommentTail

CommentTail:
    * CommentTailStar
    NotStar CommentTail

CommentTailStar:
    /
    * CommentTailStar
    NotStarNotSlash CommentTail

NotStar:
    InputCharacter but not *
    LineTerminator

NotStarNotSlash:
    InputCharacter but not * or /
    LineTerminator

EndOfLineComment:
    / / {InputCharacter}
```

These productions imply all of the following properties:

* Comments do not nest.

* /* and */ have no special meaning in comments that begin with //.

* // has no special meaning in comments that begin with /* or /**.

>As a result, the following text is a single complete comment:
>```java
>/* this comment /* // /** ends here: */
>```

The lexical grammar implies that comments do not occur within character literals (§3.10.4) or string literals (§3.10.5).