import os
import sys

from twilio.base.exceptions import TwilioRestException
from twilio.rest import Client

DEFAULT_BODY = "🚀 GitHub Actions Test: Your automated SMS pipeline is working perfectly!"
MAX_LENGTH = 1600  # Twilio's maximum message length


def get_config():
    """Read credentials and message text from environment variables."""
    config = {
        "account_sid": os.environ.get("TWILIO_ACCOUNT_SID"),
        "auth_token": os.environ.get("TWILIO_AUTH_TOKEN"),
        "from_number": os.environ.get("TWILIO_PHONE_NUMBER"),
        "to_number": os.environ.get("MY_MOBILE_NUMBER"),
    }

    missing = [name for name, value in config.items() if not value]
    if missing:
        print(f"❌ Missing environment variables for: {', '.join(missing)}")
        sys.exit(1)

    body = (os.environ.get("SMS_BODY") or DEFAULT_BODY).strip()
    if not body:
        print("❌ Message body is empty.")
        sys.exit(1)
    if len(body) > MAX_LENGTH:
        print(f"❌ Message is {len(body)} characters; the limit is {MAX_LENGTH}.")
        sys.exit(1)

    config["body"] = body
    return config


def send_sms(config):
    """Send the SMS and return the message SID."""
    client = Client(config["account_sid"], config["auth_token"])
    message = client.messages.create(
        body=config["body"],
        from_=config["from_number"],
        to=config["to_number"],
    )
    return message.sid, message.status


def main():
    config = get_config()

    try:
        sid, status = send_sms(config)
        print(f"✅ Message sent. SID: {sid} | Status: {status}")
    except TwilioRestException as e:
        print(f"❌ Twilio error {e.code}: {e.msg}")
        print(f"   More info: https://www.twilio.com/docs/errors/{e.code}")
        sys.exit(1)
    except Exception as e:
        print(f"❌ Unexpected error: {e}")
        sys.exit(1)


if __name__ == "__main__":
    main()
