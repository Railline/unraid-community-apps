# PiGallery2 Plus

PiGallery2 Plus is a performance and metadata focused fork of PiGallery2 for large self-hosted media archives.

It keeps the original directory-first gallery model and adds features aimed at large libraries, richer metadata, public sharing, and smoother browsing.

## Highlights

- Fast large-gallery browsing with incremental loading improvements.
- Rich metadata display for archive workflows, including source site, source URL, preserved filename, creator, and private-gallery markers.
- Share-link fixes for guest and limited guest browsing.
- Public random-image URLs that can be constrained by a share key and search query.
- Editable random-link queries for admins and link owners.
- Activity audit logs for logins, user actions, share-link use, and admin views.
- Wider video support through ffmpeg-backed transcoding, including MKV and other common archive formats.
- Lightbox video controls with 5 second keyboard seeking and finer volume handling.

## Recommended Unraid Setup

Keep your media mount read-only unless you specifically need PiGallery2 Plus to write to your archive.

Suggested mappings:

```text
/app/data/config -> /mnt/user/appdata/pigallery2plus/config
/app/data/db     -> /mnt/user/appdata/pigallery2plus/db
/app/data/tmp    -> /mnt/user/appdata/pigallery2plus/tmp
/app/data/images -> your media library, mounted read-only
```

For best performance, place `config`, `db`, and `tmp` on cache/SSD-backed appdata.

## First Start

Open the WebUI after the container starts and complete the PiGallery2 setup.

If you expose the gallery through a reverse proxy, set the template's `Public URL` advanced variable to the public HTTPS URL so generated share links and random-image URLs are correct.

## Docker Image

```text
railline/pigallery2plus:latest
```

Versioned tags are also available, for example:

```text
railline/pigallery2plus:3.6.0-plus.4
```

## Support

- Project: <https://github.com/Railline/pigallery2plus>
- Template issues: <https://github.com/Railline/unraid-community-apps/issues>
