# The Deprecation HTTP Response Header Field

The HTTP Deprecation response header field can be used to signal to consumers of an URI-identified resource that the use of the resource has been deprecated. Additionally, the deprecation link relation can be used to link to a resource that provides additional context for the deprecation, and possibly ways in which clients can find a replacement for the deprecated resource.

## Published draft

The latest published draft can always be found [on the IETF site](https://tools.ietf.org/html/draft-dalal-deprecation-header).

## Providing Feedback

If the draft you want to make a comment on specifies an e-mail list for feedback, please use this address. Usually, it's in the abstract.

Otherwise, feel free to [open an issue on GitHub](https://github.com/sdatspun2/deprecation-header/issues/).

## Making Contributions

If you want to submit a pull request against the draft, modify `draft.md` only. We use [kramdown-rfc2629](https://github.com/cabo/kramdown-rfc2629) to verify the content and generate markdown, plain text, and XML versions. 

```
kramdown-rfc2629 draft.md
```

or

```
kdrfc draft.md
```

Then use [xml2rfc](https://xml2rfc.tools.ietf.org) to generate .html, .pdf, etc. to see results of your work.

```
xml2rfc draft.xml —html 
```
