# Build the technical report

The report uses the IEEE conference class, native TikZ diagrams, and PGFPlots.
It has no external image dependency.

From the repository root:

```bash
tectonic report/Huntrix_Technical_Report.tex --outdir .
pdfinfo Huntrix_Technical_Report.pdf | grep Pages
```

The expected page count is `4`. Tectonic may download LaTeX packages on the
first local build; this report build is separate from the offline Kaggle
inference notebook.
