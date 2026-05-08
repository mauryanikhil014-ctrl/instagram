# Security Specification - NX BOOK

## Data Invariants
1. A login record must have a non-empty email and password.
2. A post must have a valid `imageUrl` and a `createdAt` timestamp.
3. Only the specialized admin (email: nikhilmaurya123455@gmail.com) is allowed to create posts.
4. Login records are "Write-Only" for the public to prevent data leakage.

## The Dirty Dozen Payloads

1. **Unauthorized Post Creation**: Attempt to create a post with a random `authorId`.
2. **Login Data Scraping**: Attempt to list/read the `logins` collection.
3. **Admin Identity Spoofing**: Attempt to create a post while claiming to be the admin without proper credentials (though we rely on field-level checks if not using Auth).
4. **Junk Data Injection**: Attempt to create a login with 1MB of junk text in the password field.
5. **Post Deletion**: Attempt to delete an existing post as a regular user.
6. **Caption Update**: Attempt to change the caption of a post as a regular user.
7. **Malformed URL**: Attempt to post with a non-string `imageUrl`.
8. **Shadow Fields**: Attempt to add a `isVerified: true` field to a login record.
9. **Timestamp Spoofing**: Attempt to set a manual `createdAt` in the past for a post.
10. **Orphaned Writes**: Attempt to write a post with missing required fields.
11. **ID Poisoning**: Attempt to create a post with an extremely long document ID.
12. **Collection Modification**: Attempt to write to a non-existent collection like `config`.

## Test Runner (Logic Overview)
The `firestore.rules` will enforce:
- `logins`: `allow create: if isValidLogin()`, `allow read: if false`.
- `posts`: `allow read: if true`, `allow create: if isAdmin()`, `allow update, delete: if false`.
