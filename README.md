# dog-for-a-date-app
import React, { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";

export default function DogForADateApp() {
  const [step, setStep] = useState(1);
  const [isAdmin, setIsAdmin] = useState(false);
  const [users, setUsers] = useState([]);
  const [profile, setProfile] = useState({
    name: "",
    age: "",
    hasDog: false,
    dogName: "",
    bio: "",
    idCard: null,
    socialMedia: "",
    isVerified: false,
  });

  const handleChange = (e) => {
    const { name, value, type, checked, files } = e.target;
    if (type === "file") {
      setProfile({
        ...profile,
        [name]: files[0],
      });
    } else {
      setProfile({
        ...profile,
        [name]: type === "checkbox" ? checked : value,
      });
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!profile.idCard) {
      alert("Please upload a valid ID before continuing.");
      return;
    }
    setUsers([...users, profile]);
    setStep(2);
  };

  const markAsVerified = (index) => {
    const updatedUsers = [...users];
    updatedUsers[index].isVerified = true;
    setUsers(updatedUsers);
    if (index === users.length - 1) {
      setProfile({ ...profile, isVerified: true });
    }
  };

  return (
    <div className="min-h-screen bg-beige p-6 flex flex-col items-center justify-center text-green-900">
      <h1 className="text-3xl font-bold mb-4">🐶 A Dog for a Date</h1>

      <Button
        onClick={() => setIsAdmin(!isAdmin)}
        className="mb-4 bg-gray-800 text-white"
      >
        {isAdmin ? "Switch to User View" : "Switch to Admin View"}
      </Button>

      {isAdmin ? (
        <Card className="w-full max-w-2xl bg-white text-green-900">
          <CardContent className="space-y-4">
            <h2 className="text-2xl font-semibold mb-2">👮 Admin Dashboard</h2>
            {users.length === 0 ? (
              <p>No users yet.</p>
            ) : (
              users.map((user, index) => (
                <div key={index} className="border p-4 rounded-lg">
                  <p><strong>Name:</strong> {user.name}</p>
                  <p><strong>Age:</strong> {user.age}</p>
                  <p><strong>Dog:</strong> {user.hasDog ? user.dogName : "None"}</p>
                  <p><strong>Bio:</strong> {user.bio}</p>
                  <p><strong>Social:</strong> {user.socialMedia || "N/A"}</p>
                  <p><strong>Status:</strong> {user.isVerified ? "✅ Verified" : "⏳ Pending"}</p>
                  {!user.isVerified && (
                    <Button
                      onClick={() => markAsVerified(index)}
                      className="mt-2 bg-blue-600 text-white"
                    >
                      Verify Identity
                    </Button>
                  )}
                </div>
              ))
            )}
          </CardContent>
        </Card>
      ) : step === 1 ? (
        <Card className="w-full max-w-md">
          <CardContent>
            <form onSubmit={handleSubmit} className="space-y-4">
              <Input
                name="name"
                placeholder="Your Name"
                value={profile.name}
                onChange={handleChange}
                required
              />
              <Input
                name="age"
                placeholder="Your Age"
                type="number"
                value={profile.age}
                onChange={handleChange}
                required
              />
              <label className="flex items-center space-x-2">
                <input
                  type="checkbox"
                  name="hasDog"
                  checked={profile.hasDog}
                  onChange={handleChange}
                />
                <span>I have a dog</span>
              </label>
              {profile.hasDog && (
                <Input
                  name="dogName"
                  placeholder="Dog's Name"
                  value={profile.dogName}
                  onChange={handleChange}
                />
              )}
              <Textarea
                name="bio"
                placeholder="Tell us something fun..."
                value={profile.bio}
                onChange={handleChange}
              />
              <Input
                name="socialMedia"
                placeholder="Link to your Instagram / TikTok"
                value={profile.socialMedia}
                onChange={handleChange}
              />
              <div>
                <label className="block mb-1 font-medium">Upload ID Card (PDF or Image)</label>
                <Input
                  type="file"
                  name="idCard"
                  accept=".jpg,.jpeg,.png,.pdf"
                  onChange={handleChange}
                  required
                />
              </div>
              <Button type="submit" className="w-full bg-green-700 text-white">
                Continue
              </Button>
            </form>
          </CardContent>
        </Card>
      ) : (
        <Card className="w-full max-w-md bg-white text-green-900">
          <CardContent className="space-y-4">
            <h2 className="text-xl font-semibold">Welcome, {profile.name}!</h2>
            <p>You're now part of the dog-friendly dating club 🐾</p>
            <p>
              {profile.hasDog
                ? `Your dog ${profile.dogName} will help you find love.`
                : "You'll get matched with people and maybe even a furry friend!"}
            </p>
            <p>✅ ID card uploaded successfully.</p>
            {profile.socialMedia && <p>🔗 Social media connected: {profile.socialMedia}</p>}
            {profile.isVerified ? (
              <p className="text-green-700 font-bold">✅ Your identity has been verified</p>
            ) : (
              <p className="text-yellow-600 font-semibold">⏳ Awaiting manual ID verification</p>
            )}
            <Button onClick={() => setStep(1)} className="bg-green-700 text-white">
              Edit Profile
            </Button>
          </CardContent>
        </Card>
      )}
    </div>
  );
}
