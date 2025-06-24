# ProperTree UID Reference Enhancement

## Overview
Enhanced ProperTree's plist analysis to handle UID references more intelligently:
- When a UID refers to a single value, it displays the value in parentheses next to the UID
- When a UID refers to a collection (dictionary/array), it displays as a clickable hyperlink that jumps to the referenced node

## Changes Made

### Modified Files
- `Scripts/plistwindow.py`: Added three new methods for UID reference handling

### New Methods Added

#### 1. `find_node_by_uid_reference(self, uid_index)`
- Locates a tree node by its UID index reference
- Correctly maps UID indices to elements in the `$objects` array
- Returns the corresponding tree node or None if not found

#### 2. `get_uid_reference_display(self, uid_index)`
- Formats UID display text based on the referenced content
- For single values: shows "UID X (value)"
- For collections: shows "UID X → (N items)"
- Handles edge cases like invalid indices gracefully

#### 3. `jump_to_uid_reference(self, uid_index)`
- Navigates to the node referenced by a UID
- Selects, focuses, and scrolls to the target node
- Returns True if successful, False otherwise

### Updated Methods
- `_add_node`: Now uses `get_uid_reference_display` for UID and CF$UID values
- `on_double_click`: Enhanced to handle UID value clicks and trigger navigation

## How It Works

### Apple Plist UID Format
Apple's plist format with UIDs contains:
- `$objects` array: Contains all the actual data objects
- UID references: Point to indices in the `$objects` array
- `CF$UID` dictionaries: Contain the UID index values

### Implementation Logic
1. When displaying a UID value, the code looks up the `$objects` array
2. Finds the element at the specified UID index
3. Determines if it's a single value or collection
4. Formats the display accordingly
5. When double-clicked, navigates to the referenced node in the tree

## Testing

### Test Files Created
1. `test_uid.plist`: Basic UID reference test
2. `test_uid_comprehensive.plist`: Comprehensive test with various data types
3. `test_uid_edge_cases.plist`: Edge cases (invalid, negative, out-of-bounds UIDs)

### Expected Behavior
- **Single values** (strings, numbers, booleans): Display as "UID X (value)"
- **Collections** (dictionaries, arrays): Display as "UID X → (N items)" and are clickable
- **Invalid UIDs**: Display as "UID X (invalid)" 
- **Double-clicking** on UID values should jump to the referenced node

### Testing Steps
1. Open ProperTree with any of the test files:
   ```
   python ProperTree.py test_uid_comprehensive.plist
   ```

2. Look for UID references in the tree view:
   - Single values should show the actual value in parentheses
   - Collections should show item count and be clickable

3. Double-click on UID values to test navigation:
   - Should jump to the corresponding node in the `$objects` array
   - The target node should be selected and visible

## Error Handling
- Gracefully handles invalid UID indices
- Handles missing `$objects` arrays
- Handles malformed UID data
- Falls back to displaying raw UID values when errors occur

## Compatibility
- Maintains backward compatibility with existing ProperTree functionality
- Only affects display and interaction with UID values
- Does not modify the underlying plist data structure
