# The Deprecation HTTP Header Field

The HTTP Deprecation header field can be used to signal to consumers of an URI-identified resource that the use of the resource has been deprecated. Additionally, the deprecation link relation can be used to link to a resource that provides additional context for the deprecation, and possibly ways in which clients can find a replacement for the deprecated resource.

## Providing Feedback

If the draft you want to make a comment on specifies an e-mail list for feedback, please use this address. Usually, it's in the abstract.

Otherwise, feel free to the authors [Sanjay Dalal](mailto:sanjay.dalal@cal.berkeley.edu) or [Erik Wilde](mailto:erik.wilde@dret.net) via email, or to open an issue on GitHub.

## Making Contributions

If you want to submit a pull request against a draft, modify `draft.md` only. We use [kramdown-rfc2629](https://github.com/cabo/kramdown-rfc2629) to verify the content and generate txt and xml. 

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




