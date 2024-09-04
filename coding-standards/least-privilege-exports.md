# Least Privilege Exports

Export and expose as little as possible. If you export a function and only import it in one file, consider moving the function to that file.

This also means when using barreling that the export should be explicit so that it expresses intent on what is supposed to be exported to another area, as a wildcard will also export functions that are exported only to be used in a sibling file.
