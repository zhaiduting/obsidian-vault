```shell
#!/bin/bash
# Description: Converts a plain text file to an SSML file with <mark> tags.
# Usage: txt2ssml.sh <input_txt_file> [output_ssml_file]

# Step 1: Get input filename
if [ -z "$1" ]; then
  echo "Usage: $0 <input_txt_file> [output_ssml_file]"
  exit 1
fi

input_file="$1"
output_file="${2:-${input_file%.*}.ssml}" # Default output to .ssml if not provided

# Step 2: Check if input file exists and is not empty
if [ ! -f "$input_file" ] || [ ! -s "$input_file" ]; then
  echo "Error: Input file does not exist or is empty: $input_file"
  exit 1
fi

echo "Converting '$input_file' to SSML: '$output_file'..."

# Start with the <speak> tag
echo "<speak>" > "$output_file"

# Use sed for initial punctuation replacement for portability
# Replace full-width punctuation with half-width followed by a space
sed -e 's/。/. /g' \
    -e 's/？/? /g' \
    -e 's/！/! /g' \
    -e 's/，/, /g' \
    "$input_file" | \
awk -v i=0 '{
    # This awk processes one line at a time after initial sed replacements.
    # It builds a new line (output_line) by taking chunks from the input line.

    input_line = $0;
    output_line = "";

    while (match(input_line, /([.?!,])([[:space:]]*|$)/)) {
        # text_before_punc: the part of the string before the punctuation
        text_before_punc = substr(input_line, 1, RSTART - 1);

        # punc_and_space: the matched punctuation and any trailing spaces/newline
        punc_and_space = substr(input_line, RSTART, RLENGTH);

        # Add text before punctuation to the output
        output_line = output_line text_before_punc;

        # Append the punctuation, space, and the mark tag
        output_line = output_line punc_and_space "<mark name=\x27timepoint_" (++i) "\x27/>";

        # Update input_line to only contain the remaining part *after* the matched segment
        input_line = substr(input_line, RSTART + RLENGTH);
    }

    # After the loop, append any remaining text (if no more punctuation)
    output_line = output_line input_line;

    print output_line;
}' >> "$output_file"

# End with the </speak> tag
echo "</speak>" >> "$output_file"

echo "✅ Successfully created SSML file: $output_file"
```
