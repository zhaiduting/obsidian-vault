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
output_file="${2:-${input_file%.*}.ssml}" # Default to .ssml if not provided

# Step 2: Check if input file exists and is not empty
if [ ! -f "$input_file" ] || [ ! -s "$input_file" ]; then
  echo "Error: Input file does not exist or is empty: $input_file"
  exit 1
fi

echo "Converting '$input_file' to SSML: '$output_file'..."

# Step 3: Generate SSML
echo "<speak>" > "$output_file"

cat "$input_file" | awk -v i=0 -v min_spacing=20 '
BEGIN {
    # Define regex for punctuations (including optional space)
    punct = "([.。？！、,，…!?]+[[:space:]]*)+"
}
{
    if ($0 ~ /^[[:space:]]*$/) {
        print ""; next;
    }
    input_line = $0;
    output_line = "";
    last_insert_pos = 0;
    offset = 0;
	output_line = "<mark name=\x27timepoint_" (++i) "\x27/>"

    while (match(input_line, punct)) {

        text_before = substr(input_line, 1, RSTART - 1);
        punc_block = substr(input_line, RSTART, RLENGTH);
		remaining = substr(input_line, RSTART + RLENGTH);

        output_line = output_line text_before;

        current_pos = offset + RSTART;

        if (current_pos - last_insert_pos >= min_spacing && remaining ~ /[[:alnum:]]/) {
            output_line = output_line punc_block "<mark name=\x27timepoint_" (++i) "\x27/>";
            last_insert_pos = current_pos;
        } else {
            output_line = output_line punc_block;
        }

        offset += RSTART + RLENGTH - 1;
        input_line = remaining;
    }

    output_line = output_line input_line;
    print output_line;
}
' >> "$output_file"

echo "</speak>" >> "$output_file"

echo "✅ Successfully created SSML file: $output_file"

```
