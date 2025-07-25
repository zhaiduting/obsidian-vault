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
    punct = "([.。？！、,，…!?]+[[:space:]]*[\\)）’”」』】\\]}]*[[:space:]]*)+"
}
{
    if ($0 ~ /^[[:space:]]*$/) {
        print $0; next;
    }
    input_line = $0;
    output_line = "<mark name=\x27timepoint_" (++i) "\x27/>";
    quote_count = 0;
	mark_interval = 0;

    while (match(input_line, punct)) {
        text_before = substr(input_line, 1, RSTART - 1);
        punc_block = substr(input_line, RSTART, RLENGTH);
        remaining = substr(input_line, RSTART + RLENGTH);
        output_line = output_line text_before punc_block;
        next_char = substr(remaining, 1, 1)

        # 更新半角引号计数器
        temp = text_before
		quote_count += gsub(/"/, "", temp)

		if (mark_interval + RSTART + RLENGTH < min_spacing) {
			mark_interval += RSTART + RLENGTH;
		} else {
			mark_interval = 0;
			if (length(remaining) < min_spacing) {
				output_line = output_line remaining;
				remaining = "";
			} else if (next_char != "\"" || quote_count % 2 == 0) {
				output_line = output_line "<mark name=\x27timepoint_" (++i) "\x27/>";
			} else {
				output_line = output_line next_char "<mark name=\x27timepoint_" (++i) "\x27/>";
				quote_count += 1;
				remaining = substr(remaining, 2);  # 砍掉半角引号
			}
		}

        input_line = remaining;
    }

    output_line = output_line input_line;
    print output_line;
}
' >> "$output_file"

echo "</speak>" >> "$output_file"

echo "✅ Successfully created SSML file: $output_file"

```
